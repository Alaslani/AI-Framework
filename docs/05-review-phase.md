# Phase 4: Review

> Validate before shipping.

## Purpose

Review is the quality gate between implementation and production. Even verified code needs final checks for patterns, edge cases, and integration.

---

## Workflow

```
1. SELF-REVIEW    → Check own work
2. AI-REVIEW      → Get AI perspective
3. INTEGRATION    → Verify system behavior
4. APPROVAL       → Ready to merge
```

---

## Self-Review Checklist

### Correctness
- [ ] Logic is sound
- [ ] Edge cases handled
- [ ] Error states handled
- [ ] Business logic correct

### Code Quality
- [ ] Readable and clear
- [ ] No duplication
- [ ] Single responsibility
- [ ] Proper naming

### Tests
- [ ] Tests exist for new code
- [ ] Edge cases tested
- [ ] All tests pass

### Security
- [ ] No secrets in code
- [ ] Input validated
- [ ] Output encoded
- [ ] Auth checks present

### Performance
- [ ] No N+1 queries
- [ ] Resources cleaned up
- [ ] No blocking operations

### Style
- [ ] Matches conventions
- [ ] No debug code
- [ ] No commented code

---

## AI Review Prompts

### Bug Detection
```
Review for:
1. Potential bugs
2. Edge cases not handled
3. Error scenarios
```

### Security
```
Review for security:
1. Input validation gaps
2. Auth/authz holes
3. Data exposure risks
```

### Performance
```
Review for performance:
1. N+1 patterns
2. Unnecessary computations
3. Memory leaks
```

---

## Integration Testing

```
1. Run through happy path
2. Test edge cases
3. Test error scenarios
4. Verify side effects
```

---

## Review Output

```markdown
# Review: [Feature/Task]

## Summary
[What changed]

## Self-Review
- [x] Correctness
- [x] Tests
- [x] Security
- [x] Performance

## AI Findings
| Issue | Severity | Resolution |
|-------|----------|------------|
| ... | Low/Med/High | ... |

## Integration
- [x] Happy path
- [x] Edge cases
- [x] Error handling

## Ready to Merge
- [x] All checks pass
```

---

## Common Findings

### Frequently Missed
1. Null/undefined handling
2. Empty arrays
3. Error messages
4. Loading states
5. Race conditions

### Security Blindspots
1. Input validation
2. Output encoding
3. Authorization
4. Data exposure

---

## Review Depth

| Change | Depth |
|--------|-------|
| Quick fix | Self + AI check |
| Standard | Full checklist |
| Multi-phase | Full + integration |
| Major | Full + team review |

---

## Time Investment

| Complexity | Review Time |
|------------|-------------|
| Quick fix | 5 min |
| Standard | 15-30 min |
| Multi-phase | 30-60 min |
| Major | 1-2 hours |

Review = ~10-15% of total task time.

---

## Anti-Patterns

| Anti-Pattern | Result | Fix |
|--------------|--------|-----|
| Skip review | Bugs ship | Always review |
| Rubber stamp | Issues missed | Use checklist |
| Happy path only | Errors broken | Test edge cases |
| No documentation | Issues repeat | Document findings |

---

## Next

When review complete → [Retrospective Phase](06-retrospective-phase.md)
