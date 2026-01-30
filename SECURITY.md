# Vibe Coding Security Guide

> "If the browser can see it, it's not a secret."

AI-assisted development moves fast. Security often gets overlooked. This guide covers the most common vulnerabilities in vibe-coded apps and how to fix them.

## The Ten Critical Issues

| Issue | Risk Level | How Common |
|-------|------------|------------|
| Exposed API Keys | Critical | Very common |
| Missing Authorization | Critical | Extremely common |
| Unvalidated Input | High | Common |
| No Rate Limiting | High | Very common |
| Verbose Error Messages | Medium | Common |
| CORS Misconfiguration | Medium | Common |
| Missing Security Headers | Medium | Very common |
| Dependency Vulnerabilities | High | Common |
| No CSRF Protection | Medium | Common |
| Hardcoded Admin Accounts | Critical | Occasional |

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

## 4. No Rate Limiting

### The Problem

Vibe-coded APIs accept unlimited requests:

```javascript
// ❌ NO LIMIT - Attackers can hammer this
app.post('/api/login', (req, res) => {
  return authenticate(req.body);
});
```

This enables:
- Brute force password attacks
- API abuse running up your costs
- Denial of service

### The Fix

```javascript
// ✅ RATE LIMITED
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, try again later'
});

app.use('/api/', limiter);

// Stricter for auth endpoints
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 5, // 5 attempts
});

app.post('/api/login', authLimiter, (req, res) => {
  return authenticate(req.body);
});
```

---

## 5. Verbose Error Messages

### The Problem

Detailed errors help attackers:

```javascript
// ❌ LEAKY - Reveals system info
app.use((err, req, res, next) => {
  res.status(500).json({
    error: err.message,
    stack: err.stack,
    query: req.query,
    dbConnection: process.env.DATABASE_URL
  });
});
```

### The Fix

```javascript
// ✅ SAFE - Generic public errors, detailed logs
app.use((err, req, res, next) => {
  // Log full error internally
  console.error('Internal error:', err);

  // Return generic message to user
  res.status(500).json({
    error: 'Something went wrong',
    requestId: req.id // For support lookup
  });
});
```

**Rules:**
- Never expose stack traces in production
- Never reveal database structure
- Never show internal paths
- Log details server-side only

---

## 6. CORS Misconfiguration

### The Problem

Wildcard CORS allows any site to call your API:

```javascript
// ❌ WIDE OPEN - Any website can access
app.use(cors({ origin: '*' }));
```

### The Fix

```javascript
// ✅ RESTRICTED - Only your domains
app.use(cors({
  origin: [
    'https://yourapp.com',
    'https://www.yourapp.com',
    process.env.NODE_ENV === 'development' && 'http://localhost:3000'
  ].filter(Boolean),
  credentials: true
}));
```

---

## 7. Missing Security Headers

### The Problem

Default headers leave browsers vulnerable. Without security headers, your app is susceptible to:
- Clickjacking (no X-Frame-Options)
- MIME sniffing attacks (no X-Content-Type-Options)
- XSS attacks (no X-XSS-Protection)

### The Fix

```javascript
// ✅ Add security headers
import helmet from 'helmet';

app.use(helmet());

// Or manually:
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000');
  next();
});
```

---

## 8. Dependency Vulnerabilities

### The Problem

AI often suggests outdated packages with known vulnerabilities:

```json
// ❌ Old version with CVEs
"dependencies": {
  "lodash": "4.17.15"
}
```

### How to Check

```bash
# npm
npm audit

# yarn
yarn audit

# pnpm
pnpm audit
```

### The Fix

```bash
# Auto-fix what's possible
npm audit fix

# Update specific package
npm update lodash

# Check for major updates
npx npm-check-updates
```

**Make this routine:**
- Run `npm audit` before every deploy
- Set up GitHub Dependabot
- Review AI-suggested packages

---

## 9. No CSRF Protection

### The Problem

Without CSRF tokens, attackers can trick users into actions:

```html
<!-- Malicious site -->
<form action="https://yourapp.com/api/transfer" method="POST">
  <input type="hidden" name="to" value="attacker" />
  <input type="hidden" name="amount" value="1000" />
</form>
<script>document.forms[0].submit()</script>
```

### The Fix

```javascript
// ✅ Require CSRF token
import csrf from 'csurf';

app.use(csrf({ cookie: true }));

app.get('/form', (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});
```

```html
<form action="/transfer" method="POST">
  <input type="hidden" name="_csrf" value="{{csrfToken}}" />
  <!-- form fields -->
</form>
```

**For SPAs:** Use SameSite cookies + custom headers

---

## 10. Hardcoded Admin Accounts

### The Problem

```javascript
// ❌ CRITICAL - Obvious backdoor
if (username === 'admin' && password === 'admin123') {
  grantAdminAccess();
}

// ❌ Also bad - in config files
const ADMIN_PASSWORD = 'supersecret';
```

### The Fix

- Never hardcode credentials
- Use proper user management
- Admin accounts via secure setup process
- Rotate credentials regularly

```javascript
// ✅ Proper admin check
const isAdmin = await db.users.findOne({
  id: req.session.userId,
  role: 'admin'
});
```

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

### Rate Limiting & DoS Protection
- [ ] Auth endpoints rate limited (5-10 attempts/hour)
- [ ] API endpoints rate limited (100-1000/minute based on use case)
- [ ] File upload size limits enforced

### Error Handling
- [ ] No stack traces in production responses
- [ ] No database/system info in errors
- [ ] Errors logged server-side with request ID

### Headers & CORS
- [ ] Security headers set (use helmet or manual)
- [ ] CORS restricted to your domains only
- [ ] SameSite cookie attribute set

### Dependencies & Config
- [ ] `npm audit` shows 0 critical/high vulnerabilities
- [ ] No hardcoded admin accounts
- [ ] No test/debug credentials in codebase
- [ ] CSRF protection enabled for forms

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
| No rate limiting | AI doesn't add it by default | Add express-rate-limit |
| Detailed error messages | Debugging left enabled | Use generic errors in production |
| CORS set to `*` | Quick fix during development | Restrict to your domains |
| Missing security headers | Not visible, easy to forget | Use helmet.js |
| Outdated dependencies | AI suggests what it knows | Run npm audit regularly |

---

## Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — Most critical web security risks
- [Supabase RLS Guide](https://supabase.com/docs/guides/auth/row-level-security) — Database-level security
- [Zod](https://zod.dev/) — TypeScript-first input validation
- [GitGuardian](https://www.gitguardian.com/) — Scan repos for exposed secrets

---

*Remember: Moving fast is great. Moving fast securely is better.*
