# Vibe Coding Security Guide

> "If the browser can see it, it's not a secret."

AI-assisted development moves fast. Security often gets overlooked. This guide covers the most common vulnerabilities in vibe-coded apps and how to fix them.

## The Three Critical Issues

| Issue | Risk Level | How Common |
|-------|------------|------------|
| Exposed API Keys | Critical | Very common |
| Missing Authorization | Critical | Extremely common |
| Unvalidated Input | High | Common |

---

## 1. Exposed API Keys & Credentials

### The Problem

AI-generated code often hard-codes secrets directly into files:

```javascript
// ❌ EXPOSED - Anyone can see this
const apiKey = "sk-1234567890abcdef";
const dbPassword = "super_secret_123";
```

If it's in your frontend code, **it's public**. Browser dev tools, page source, and network requests expose everything.

### How to Check

**Step 1: AI Audit**
```
Review my code and identify any API keys, secrets, tokens,
or credentials that may be exposed. Flag anything that
should be moved to environment variables.
```

**Step 2: Browser Check**
- Right-click → View Page Source → Search for "key", "secret", "password"
- Dev Tools → Network tab → Check request headers/bodies

**Step 3: Repo History**

Even if you deleted it, Git remembers. Search your commits:
```bash
git log -p | grep -i "api_key\|secret\|password"
```

### The Fix

```javascript
// ✅ SAFE - Use environment variables
const apiKey = process.env.API_KEY;
const dbPassword = process.env.DB_PASSWORD;
```

**If already exposed:**
1. Generate NEW keys immediately
2. Move to environment variables
3. Revoke the old keys
4. Redeploy

Simply deleting the code doesn't fix it — the old key is still compromised.

---

## 2. Authentication ≠ Authorization

### The Problem

> "Logging in is like showing ID at a front desk. Authorization is what locks the doors inside."

Many vibe-coded apps let users log in but don't actually protect data:

```javascript
// ❌ INSECURE - Only checks if logged in, not WHO
app.get('/api/user/:id', (req, res) => {
  if (req.session.loggedIn) {
    return db.getUser(req.params.id); // Any user can get ANY user's data!
  }
});
```

### The Test

Ask yourself: **What stops User A from seeing User B's data?**

If your answer is:
- "The UI doesn't show it" → ❌ Not protected
- "There's no button for it" → ❌ Not protected
- "The backend checks user ID" → ✅ Protected

### The Fix

```javascript
// ✅ SECURE - Verify ownership
app.get('/api/user/:id', (req, res) => {
  if (req.session.userId !== req.params.id) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  return db.getUser(req.params.id);
});
```

**For Supabase users:** Enable Row Level Security (RLS)

```sql
-- ✅ Users can only see their own data
CREATE POLICY "Users see own data" ON users
  FOR SELECT USING (auth.uid() = id);
```

### AI Audit Prompt

```
Audit my backend and database rules. Explain what actually
enforces access control. Identify any endpoints where a
logged-in user could access another user's data.
```

---

## 3. Unvalidated Input

### The Problem

Vibe-coded apps trust user input too much:

```javascript
// ❌ DANGEROUS - No validation
app.post('/upload', (req, res) => {
  saveFile(req.file); // Could be malware!
});

app.post('/api/query', (req, res) => {
  db.query(req.body.sql); // SQL injection!
});
```

### The Fix

```javascript
// ✅ SAFE - Validate everything
import { z } from 'zod';

const uploadSchema = z.object({
  file: z.instanceof(File),
  size: z.number().max(5_000_000), // 5MB limit
  type: z.enum(['image/png', 'image/jpeg', 'application/pdf'])
});

app.post('/upload', (req, res) => {
  const validated = uploadSchema.parse(req.body);
  saveFile(validated.file);
});
```

**Rules:**
- Whitelist allowed file types
- Set size limits
- Sanitize text input
- Never pass raw input to database queries
- Use parameterized queries

---

## Security Checklist for Vibe Coders

Before deploying, verify:

### API Keys & Secrets
- [ ] No secrets in frontend code
- [ ] All secrets in environment variables
- [ ] `.env` files in `.gitignore`
- [ ] No secrets in Git history

### Authorization
- [ ] Backend verifies user owns requested data
- [ ] Database has row-level security (if applicable)
- [ ] API endpoints check permissions, not just login status
- [ ] Tested: Can User A access User B's data? (Should fail)

### Input Validation
- [ ] All user input validated server-side
- [ ] File uploads restricted by type and size
- [ ] No raw SQL queries with user input
- [ ] Error messages don't expose system details

---

## AI Prompts for Security Audits

### Full Security Review

```
Perform a security audit on my codebase. Check for:
1. Exposed API keys, secrets, or credentials
2. Missing authorization checks (can users access others' data?)
3. Unvalidated user input
4. SQL injection vulnerabilities
5. Sensitive data in logs or error messages

For each issue found, explain the risk and provide a fix.
```

### Quick Pre-Deploy Check

```
I'm about to deploy. Quickly check for critical security issues:
- Any hard-coded secrets?
- Any endpoints missing auth checks?
- Any unvalidated input going to database?
```

---

## Key Principles

| Principle | Meaning |
|-----------|---------|
| **Defense in depth** | Multiple layers of security, not just one |
| **Least privilege** | Users only access what they need |
| **Trust nothing** | Validate all input, verify all requests |
| **Fail secure** | When in doubt, deny access |

---

## Common Vibe Coding Security Mistakes

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| API key in frontend | AI puts it where it's needed | Move to backend, use env vars |
| No RLS policies | Didn't know it was needed | Enable RLS, add policies |
| Auth check in UI only | Assumed backend was protected | Add backend authorization |
| Trusting user input | AI doesn't validate by default | Add Zod/Yup validation |
| Secrets in Git | Committed before .gitignore | Rotate secrets, clean history |

---

## Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — Most critical web security risks
- [Supabase RLS Guide](https://supabase.com/docs/guides/auth/row-level-security) — Database-level security
- [Zod](https://zod.dev/) — TypeScript-first input validation
- [GitGuardian](https://www.gitguardian.com/) — Scan repos for exposed secrets

---

*Remember: Moving fast is great. Moving fast securely is better.*
