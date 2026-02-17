# Validation Gates

Quality checkpoints between phases.

## The Gate Principle

Never proceed to the next phase without validating the current one.

```
[Research] ──GATE──► [Plan] ──GATE──► [Implement] ──GATE──► [Deploy]
              │              │                 │
              ▼              ▼                 ▼
         "Correct?"    "Sound?"         "Working?"
```

## Human Review is the Point

Gates aren't just AI self-checks — they're **human review points**.

| Review Stage | Cost to Review | Cost of Errors |
|--------------|----------------|----------------|
| Research | Low (reading) | 1000s bad lines prevented |
| Plan | Medium (logic) | 100s bad lines prevented |
| Code | High (understand + test) | 1 bad line caught |

**The earlier you catch problems, the less wrong code gets written.**

### Review Checklist

Before approving research:
- [ ] Does it answer the original question?
- [ ] Are file paths specific with line numbers?
- [ ] Is the data flow clear?

Before approving plan:
- [ ] Does each phase have clear success criteria?
- [ ] Are the phases correctly ordered?
- [ ] Are edge cases addressed?

Before approving code:
- [ ] Does it match the plan?
- [ ] Do tests pass?
- [ ] Is it the minimal change needed?

## Gate Types

### Learning Gate (Before Planning External Integrations)

**Question:** "Do I know how this external system actually behaves?"

**Checklist:**
- [ ] I've run learning tests against the actual API/SDK
- [ ] Assumptions are marked ✅, ❌, or ⚠️
- [ ] No ❌ assumptions remain unresolved
- [ ] Key findings are documented

**When to skip:** You own the system or it's well-documented and deterministic.

See [Learning Tests](learning-tests.md) for the full /learn workflow.

### Understanding Gate (After Research)

**Question:** "Is my understanding correct?"

**Checklist:**
- [ ] I can explain the current behavior
- [ ] I know which files are involved
- [ ] I understand the data flow
- [ ] I've identified dependencies
- [ ] I've noted constraints

### Approach Gate (After Planning)

**Question:** "Is this approach sound?"

**Checklist:**
- [ ] Phases are clearly defined
- [ ] Each phase has a single goal
- [ ] Files per phase are bounded
- [ ] Verification steps are defined
- [ ] Edge cases are considered

### Implementation Gate (After Each Phase)

**Question:** "Is this working?"

**Verification Protocol:**
```bash
# 1. Type check
npm run type-check  # or equivalent

# 2. Lint
npm run lint  # or equivalent

# 3. Build
npm run build  # or equivalent

# 4. Test
npm run test  # or manual verification
```

### Back Pressure Requirement

Each phase should declare its back pressure upfront — the specific commands
that prove the phase works. See [Back Pressure Engineering](back-pressure.md)
for the full pyramid.

Weak: "Verify it works"
Strong: "`npm run type-check && npm run build && npm run test`"

### Completion Gate (Before Deployment)

**Question:** "Is this ready?"

**Checklist:**
- [ ] All phases complete
- [ ] All tests passing
- [ ] Manual testing done
- [ ] Documentation updated
- [ ] Learnings captured

## Red Flags

Stop and reassess if:

- 🚩 Phase taking longer than expected
- 🚩 Scope creeping beyond plan
- 🚩 Unexpected dependencies discovered
- 🚩 Tests failing without clear cause
- 🚩 Context getting polluted (>60%)

## Recovery Protocol

When a gate fails:

1. **Stop** — Don't push forward
2. **Diagnose** — What went wrong?
3. **Decide** — Fix now or revise plan?
4. **Document** — Capture the learning
5. **Proceed** — With corrected approach

---

*Next: [Session Handoff](../patterns/session-handoff.md)*
