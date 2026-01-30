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

## Gate Types

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
