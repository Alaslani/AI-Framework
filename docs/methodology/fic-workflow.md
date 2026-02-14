# FIC Workflow

**Find → Implement → Compound**

The core workflow for AI-assisted development.

## Overview

```
                    ┌─────────────────┐
                    │      TASK       │
                    └────────┬────────┘
                             │
                             ▼
         ┌───────────────────────────────────┐
         │              FIND                 │
         │   Understand files, data flow,    │
         │   dependencies, constraints       │
         └───────────────────┬───────────────┘
                             │
                             ▼
                    ┌────────────────┐
                    │   GATE: OK?    │───No──→ Back to Find
                    └────────┬───────┘
                             │ Yes
                             ▼
         ┌───────────────────────────────────┐
         │           IMPLEMENT               │
         │   Plan → Execute → Verify         │
         │   (one phase at a time)           │
         └───────────────────┬───────────────┘
                             │
                             ▼
                    ┌────────────────┐
                    │   GATE: OK?    │───No──→ Back to Implement
                    └────────┬───────┘
                             │ Yes
                             ▼
         ┌───────────────────────────────────┐
         │           COMPOUND                │
         │   Capture learnings, update       │
         │   knowledge base                  │
         └───────────────────┬───────────────┘
                             │
                             ▼
              ┌──────────────────────────┐
              │  Knowledge feeds next    │
              │  Find phase              │
              └──────────┬───────────────┘
                         │
                         └──────→ (Next Task)
```

### Key Points

1. **Gates are mandatory** — Don't skip verification
2. **Phases are sequential** — Don't parallelize Find and Implement
3. **Compound closes the loop** — Knowledge feeds future work
4. **Context resets between tasks** — Fresh start with handoff docs

## Phase 1: Find (Research)

### Purpose
Understand the problem space before writing code.

### Activities
- Read relevant files
- Trace data flow
- Identify dependencies
- Note constraints

### Output
`research/[feature-name].md` with:
- Current state
- Key files involved
- Data flow diagram
- Open questions

### Research Output Format

Good research enables implementation without re-searching. Always include:

| Required | Example |
|----------|---------|
| File path | `src/lib/auth.ts` |
| Line numbers | `:45-67` |
| Purpose | "Token validation" |

**Template:**
```markdown
## File Map
| File | Lines | Purpose |
|------|-------|---------|
| `src/lib/auth.ts` | 45-67 | Token validation |
| `src/app/api/login/route.ts` | 12-34 | Login endpoint |

## Data Flow
User → LoginForm → /api/login → auth.ts:validateToken() → DB

## Key Functions
- `validateToken()` at auth.ts:45 — validates JWT
- `createSession()` at auth.ts:89 — creates user session
```

**Why line numbers matter:** The implementing agent can jump directly to the right location instead of searching again, keeping context clean.

### Checkpoint
Ask: "Is my understanding correct?" before proceeding.

## Phase 2: Implement

### Planning
Before coding:
- Break into phases (max 3-5)
- Identify files to modify per phase
- Define verification for each phase

### Execution Rules
- One phase at a time
- Keep context under 40% (heuristic, not law — large audits may need 60%+, use explicit PROGRESS.md files)
- Verify after each phase
- Write progress manually (see below)

### Intentional Compaction

❌ **Don't rely on automatic context compaction** — it loses important context.

✅ **Do write explicit progress files:**

| File | When | Purpose |
|------|------|---------|
| PROGRESS.md | During work | Checkpoint within a feature |
| Transfer Pack | End of session | Handoff to next session |
| Plan updates | After each phase | Mark "✅ Done" on completed items |

**Why manual > automatic:**
1. Forces you to synthesize what matters
2. Creates reusable artifacts
3. Gives you control over what's preserved
4. Results in better onboarding for fresh contexts

### Verification Protocol
After each phase:
```bash
1. Type check
2. Lint
3. Build
4. Test (manual or automated)
```

## Phase 3: Compound

### Capture Learnings
After implementation, document:
- What worked
- What didn't work
- Why (root cause)
- Pattern to reuse or avoid

### Update Knowledge Base
Add to MASTER_REFERENCE:
- New learnings (numbered)
- Updated decisions
- New gotchas

### Compound Checklist

Answer each before closing your session:

1. **New Rule?** Did this introduce a pattern we should always follow?
   - If yes: Add to MASTER_REFERENCE
2. **Non-Obvious Learning?** Did we discover something surprising?
   - If yes: Add to MASTER_REFERENCE with context
3. **Repeat Risk?** Would this mistake happen again without documentation?
   - If yes: Add to MASTER_REFERENCE or anti-patterns
4. **Architecture Impact?** Does this affect future design decisions?
   - If yes: Log as decision with rationale
5. **Security Implication?** Did we touch auth, permissions, or data access?
   - If yes: Verify + document in security notes

## Thinking Depth Keywords

| Keyword | Use When |
|---------|----------|
| "think" | Basic reasoning, simple tasks |
| "think hard" | Complex logic, architecture decisions |
| "think harder" | Multi-system changes, edge cases |
| "ultrathink" | Security, payments, critical systems |

## Severity Levels

Match effort to risk:

| Level | Examples | Thinking | Verification | Documentation |
|-------|----------|----------|--------------|---------------|
| **Low** | UI tweaks, copy, config | "think" | Type check | Commit message |
| **Medium** | Logic changes, refactors | "think hard" | + Lint + Build | Brief notes |
| **High** | Auth, payments, data | "think harder" | + Manual test | Full FIC |
| **Critical** | Security, money, permissions | "ultrathink" | + Code review | FIC + ADR |

**Rule**: When in doubt, go one level higher.

## When to Use Full FIC

| Situation | Workflow |
|-----------|----------|
| Quick fix (<50 lines) | Skip to Implement |
| Clear task, single file | Brief plan → Implement |
| New feature, multiple files | Full FIC |
| Unknown territory | Full FIC with extra Research |

---

*Next: [Phased Development](phased-development.md)*
