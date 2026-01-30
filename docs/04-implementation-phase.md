# Phase 3: Implementation

> Execute with continuous validation.

## Purpose

With good research and planning, implementation becomes mechanical execution with verification checkpoints.

---

## Workflow

```
For each phase:

1. LOAD     → Plan + relevant files only
2. EXECUTE  → Complete phase tasks
3. VERIFY   → Type check, lint, test
4. UPDATE   → Mark progress in plan
5. COMPACT  → Reset if context > 40%
```

---

## Context Management

### Include
✅ Current phase tasks
✅ Files being modified
✅ Relevant types/interfaces
✅ Test files

### Exclude
❌ Completed phases
❌ Exploration notes
❌ Full research docs
❌ Unrelated code

---

## Verification Protocol

**After every change:**
```
1. Type check  → tsc --noEmit / equivalent
2. Lint        → eslint / equivalent
3. Build       → build command
4. Test        → test command
```

**After every phase:**
```
1. Manual verification per plan
2. Update plan with ✅/❌
3. Document learnings
```

---

## Phase Completion

```markdown
## Phase [N] Complete

### Completed
- [x] Task 1
- [x] Task 2

### Verification
- [x] Type check passes
- [x] Tests pass
- [x] Manual: [describe]

### Learnings
[Anything unexpected]

### Ready for Phase [N+1]
```

---

## Context Compaction

When context exceeds 40%:

1. **Save** — Update plan with status
2. **Note** — Document current position
3. **Reset** — Start fresh session
4. **Resume** — Load only next phase needs

### Compaction Template

```markdown
# Context Transfer

## Status
Phase [N] complete, starting Phase [N+1]

## Key State
- [Critical context 1]
- [Critical context 2]

## Next Steps
1. [Immediate action]
2. [Following action]

## Files Changed
- path/file.ts — [what changed]
```

---

## Error Handling

### Build Fails
1. Read error message
2. Trace to source
3. Fix one at a time
4. Re-verify

### Tests Fail
1. Understand what's tested
2. Is test or code wrong?
3. Fix appropriately
4. Run full suite

### Stuck (3+ attempts)
1. Stop
2. Document what tried
3. Reset context
4. Different approach

---

## Tips

### For Clean Code
- Follow existing patterns
- Match surrounding style
- Use existing utilities
- Don't over-engineer

### For Speed
- One phase at a time
- Clear verification steps
- Fast feedback loops
- Compact when needed

### For Quality
- Type everything
- Write tests alongside
- Verify each step
- Document decisions

---

## Anti-Patterns

| Anti-Pattern | Result | Fix |
|--------------|--------|-----|
| Long unverified runs | Cascading errors | Verify each step |
| Polluted context | Bad output | Clear between phases |
| Skip verification | Subtle bugs | Always check |
| No progress tracking | Lost work | Update plan |

---

## Time Investment

| Complexity | Implementation Time |
|------------|---------------------|
| Quick fix | 5-15 min |
| Standard | 30-90 min |
| Multi-phase | 2-4 hours |
| Major | Days |

Implementation = ~50-60% of total task time.

---

## Next

When all phases complete → [Review Phase](05-review-phase.md)
