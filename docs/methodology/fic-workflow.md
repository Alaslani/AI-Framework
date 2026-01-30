# FIC Workflow

**Find → Implement → Compound**

The core workflow for AI-assisted development.

## Overview

```
┌─────────────────────────────────────────────────────┐
│  1. FIND (Research)                                 │
│     ├── Understand: files, data flow, dependencies  │
│     ├── Output: research/[feature].md               │
│     └── CHECKPOINT: "Understanding correct?"        │
│                      ↓                              │
│  2. IMPLEMENT (Plan + Execute)                      │
│     ├── Plan: phases, files, verification           │
│     ├── Execute: one phase at a time                │
│     ├── Verify: after each phase                    │
│     └── CHECKPOINT: "Working as expected?"          │
│                      ↓                              │
│  3. COMPOUND (Learn)                                │
│     ├── Capture: what worked, what didn't           │
│     └── Update: knowledge base                      │
└─────────────────────────────────────────────────────┘
```

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

❌ **Don't use `/compact`** — It's automatic and loses important context.

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

## Thinking Depth Keywords

| Keyword | Use When |
|---------|----------|
| "think" | Basic reasoning, simple tasks |
| "think hard" | Complex logic, architecture decisions |
| "think harder" | Multi-system changes, edge cases |
| "ultrathink" | Security, payments, critical systems |

## When to Use Full FIC

| Situation | Workflow |
|-----------|----------|
| Quick fix (<50 lines) | Skip to Implement |
| Clear task, single file | Brief plan → Implement |
| New feature, multiple files | Full FIC |
| Unknown territory | Full FIC with extra Research |

---

*Next: [Phased Development](phased-development.md)*
