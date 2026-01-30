# Context Management

> Keeping AI productive with clean, focused context.

## Why Context Matters

AI quality degrades as context grows:

| Context Level | Quality |
|---------------|---------|
| Under 40% | Optimal reasoning |
| 40-60% | Good but declining |
| 60-80% | Noticeably worse |
| Over 80% | Unreliable |

---

## The 40% Rule

**Keep context under 40% capacity.**

Benefits:
- Better reasoning
- More accurate code
- Faster responses
- Fewer hallucinations

---

## What to Include

✅ Current task requirements
✅ Files being modified
✅ Relevant types/interfaces
✅ Test files for verification
✅ Error messages (debugging)

## What to Exclude

❌ Completed phase code
❌ Exploration notes
❌ Full research documents
❌ Unrelated files
❌ Old chat history

---

## Strategies

### 1. One Phase Per Context

For complex tasks:
```
Session 1: Research → Document
Session 2: Planning → Write plan
Session 3: Phase 1 implementation
Session 4: Phase 2 implementation
```

### 2. File-Based Memory

Don't keep state in chat. Write to files:
```
research/feature.md  → Findings
plans/feature.md     → Plan
progress.md          → Current state
```

Reference files, don't repeat content:
```
"Continue from plans/feature.md, Phase 2"
```

### 3. Intentional Compaction

Before context gets full:
1. Document current state
2. Note what's complete
3. List next steps
4. Start fresh session

---

## Compaction Template

```markdown
# Context Transfer

## Status
Phase [N] complete, starting [N+1]

## Key State
- [What's working]
- [What's not done]

## Next Steps
1. [Immediate action]
2. [Following action]

## Files Changed
- path/file — [change]
```

---

## Clear Between Tasks

Different tasks = different contexts.

After completing a task:
1. Document learnings
2. Clear context
3. Start next task fresh

---

## Warning Signs

| Sign | Cause | Fix |
|------|-------|-----|
| Repeating itself | Context full | Reset |
| Mixing concepts | Too many topics | Separate |
| Forgetting details | Pushed out | Reference files |
| Generic responses | Noise | Reduce context |
| Hallucinating | Confusion | Start fresh |

---

## Practical Patterns

### Research Session
```
Purpose: Understand codebase
Include: Files to explore
Exclude: Everything else
Output: research.md
```

### Implementation Session
```
Purpose: Complete Phase N
Include: Plan + phase files only
Exclude: Research, other phases
Output: Working code
```

### Debug Session
```
Purpose: Fix specific bug
Include: Error + relevant code
Exclude: Unrelated code
Output: Fix + test
```

---

## Context Budgeting

| Task Type | Budget |
|-----------|--------|
| Quick fix | 10-20% |
| Standard | 20-30% |
| Complex phase | 30-40% |
| Full context | ⚠️ Compact now |

---

## Anti-Patterns

| Anti-Pattern | Result | Fix |
|--------------|--------|-----|
| Keep everything | Degraded output | Remove unneeded |
| "I'll remember" | Lost state | Write to files |
| One session | Pollution | Separate tasks |
| Paste all code | No room | Only modified files |

---

*Clean context = quality output. Be intentional about what AI sees.*
