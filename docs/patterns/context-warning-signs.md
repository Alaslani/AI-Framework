# Context Warning Signs

Know when to stop and start fresh.

## The Red Flag

When your AI says any of these, **stop immediately**:

- "Let me try a different approach"
- "I'll attempt another method"
- "Let me reconsider"
- "Perhaps we should try..."

This usually means:
1. Context is polluted with failed attempts
2. The problem is being approached wrong
3. The AI is spiraling, not converging

## When to Start Fresh

| Signal | Action |
|--------|--------|
| AI loops on same error 3+ times | Stop → Save progress → Fresh context |
| Context utilization > 60% | Compact → New session |
| "I'll try a different approach" | Stop → Review plan → Fresh context |
| Back-and-forth for 10+ messages | Stop → Write PROGRESS.md → Fresh context |
| You're frustrated/shouting at AI | Stop → The plan was bad |

## What to Save Before Starting Fresh

1. **What worked** — Keep the successful parts
2. **What failed** — Document so you don't repeat
3. **Current understanding** — File paths, line numbers, data flow
4. **Next step** — What you want the fresh agent to do first

## The Recovery Pattern

```
1. STOP   - Don't send another message
2. SAVE   - Write a PROGRESS.md with current state
3. FRESH  - Start new context window
4. ONBOARD - Paste PROGRESS.md as context
5. CONTINUE - Resume from last known good state
```

## Key Insight

> "If you're shouting at Claude, the plan was bad."
> — Adapted from Dexter Horthy

Bad research leads to bad plans. Bad plans lead to bad code. If implementation is failing, the problem is usually upstream.

## Prevention

- Keep context under 40%
- Review research before planning
- Review plans before implementing
- Write explicit progress files (not /compact)

---

*Back to: [Session Handoff](session-handoff.md)*
