# Compound Checklist

Use at the end of every session. Takes 2 minutes.

---

## Questions

Answer each before closing:

### 1. New Rule?

Did this change introduce a pattern we should always follow?

→ If yes: Add to MASTER_REFERENCE

### 2. Non-Obvious Learning?

Did we discover something surprising or counterintuitive?

→ If yes: Add to MASTER_REFERENCE with context

### 3. Repeat Risk?

Would this mistake happen again without documentation?

→ If yes: Add to MASTER_REFERENCE or anti-patterns

### 4. Architecture Impact?

Does this affect future design decisions?

→ If yes: Log as decision with rationale

### 5. Security Implication?

Did we touch auth, permissions, or data access?

→ If yes: Verify + document in security notes

---

## Quick Template

```markdown
## Session Learnings

### New Rules
- [Rule]: [Why]

### Gotchas Discovered
- [Issue]: [Solution]

### Decisions Made
- [Decision]: [Rationale]
```

---

## Why This Matters

- Compound is what separates "fast" from "sustainable"
- 2 minutes now saves 20 minutes next session
- Knowledge that isn't written is knowledge that's lost

---

*If you skip Compound, you're paying interest on technical debt you don't even know you have.*
