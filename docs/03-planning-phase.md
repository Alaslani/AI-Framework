# Phase 2: Planning

> Define what to build before building it.

## Purpose

Planning separates **"what"** from **"how"** from **"do"**. Bad plans lead to hundreds of bad lines of code. Good plans enable confident execution.

---

## The Spec → Tech → Steps Model

```
SPEC (What)
├── Business requirements
├── Success criteria
└── Constraints
      ↓
TECH (How)
├── Architecture approach
├── Key decisions
└── File changes
      ↓
STEPS (Do)
├── Ordered tasks
├── Dependencies
└── Verification points
```

---

## Workflow

```
1. SPEC     → Define what success looks like
2. TECH     → Choose the approach
3. STEPS    → Break into tasks
4. VERIFY   → Is this approach sound?
```

---

## Output Format

```markdown
# Plan: [Feature/Task]

## Objective
[One sentence goal]

## Success Criteria
- [ ] [Measurable outcome 1]
- [ ] [Measurable outcome 2]

## Technical Approach

### Key Decisions
| Decision | Choice | Rationale |
|----------|--------|-----------|
| ... | ... | ... |

### Files to Modify
| File | Change | Description |
|------|--------|-------------|
| ... | Create/Modify | ... |

## Implementation Phases

### Phase 1: [Name]
- [ ] Task 1.1
- [ ] Task 1.2
- **Verify:** [How to verify]

### Phase 2: [Name]
- [ ] Task 2.1
- [ ] Task 2.2
- **Verify:** [How to verify]

## Risks
| Risk | Mitigation |
|------|------------|
| ... | ... |

## Out of Scope
[What this doesn't include]
```

---

## Phase Sizing

Good phase:
- 1-3 files changed
- Independently verifiable
- Natural stopping point
- Fits in one context window

Bad phase:
- Too many files
- No clear verification
- Depends on future phases

---

## Decision Framework

For each technical decision:

1. **Options** — What are the choices?
2. **Tradeoffs** — What does each cost/provide?
3. **Recommendation** — Which and why?
4. **Reversibility** — How hard to change later?

---

## Quick Task Shortcut

For smaller tasks (<50 lines):

```markdown
# Quick Task: [Name]

## Goal
[One sentence]

## Steps
1. [Step 1]
2. [Step 2]

## Verify
[How to confirm]
```

---

## Checkpoint

Before proceeding to implementation:

- [ ] Objective is measurable
- [ ] Approach validated by research
- [ ] Each phase has verification
- [ ] Risks identified
- [ ] Scope is clear

---

## Time Investment

| Complexity | Planning Time |
|------------|---------------|
| Quick fix | 0-5 min |
| Standard | 15-30 min |
| Multi-phase | 30-60 min |
| Major | 1-3 hours |

Planning = ~15-25% of total task time.

---

## Anti-Patterns

| Anti-Pattern | Result | Fix |
|--------------|--------|-----|
| Over-planning | Wasted time | Diminishing returns after 200 lines |
| Under-planning | Rework | At minimum: steps + verification |
| No verification | Errors compound | Every phase needs check |
| Vague criteria | Unclear when done | Measurable outcomes |

---

## Next

When plan is complete → [Implementation Phase](04-implementation-phase.md)
