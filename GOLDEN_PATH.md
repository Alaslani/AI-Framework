# Golden Path (Cheat Sheet)

Quick reference for daily AI-assisted development.

---

## Session Start

```
1. Read MASTER_REFERENCE
2. Read TRANSFER_PACK (if exists)
3. State task clearly
4. Pick workflow (see below)
```

## Session End

```
1. Update TRANSFER_PACK
2. Add learnings to MASTER_REFERENCE
3. Note next steps
```

---

## Workflow Selection

| Task | Workflow |
|------|----------|
| Quick fix (<50 lines) | Direct implement |
| Single file, clear scope | Brief plan → implement |
| Multi-file feature | Full FIC |
| Unknown territory | FIC + extra Find phase |

---

## FIC Workflow

```
FIND → IMPLEMENT → COMPOUND

Find:      Understand files, data flow, dependencies
Implement: Plan (max 3-5 phases) → Execute → Verify
Compound:  Capture learnings → Update knowledge base
```

---

## Context Management

| Rule | Action |
|------|--------|
| Target | Keep under 40% |
| Compact when | > 60% or context polluted |
| Reset when | Same error 3x, "try different approach", 10+ back-and-forth |

> 40% is a heuristic — exceed for audits/migrations with explicit progress files.

---

## Thinking Keywords

| Keyword | Use For |
|---------|---------|
| "think" | Simple tasks |
| "think hard" | Architecture decisions |
| "think harder" | Multi-system changes |
| "ultrathink" | Security, payments |

---

## Verification (After Every Phase)

```bash
1. Type check
2. Lint
3. Build
4. Test
```

---

## Files

| File | Purpose | When to Update |
|------|---------|----------------|
| MASTER_REFERENCE | Project knowledge | After learnings |
| TRANSFER_PACK | Session handoff | End of session |
| ROADMAP | Priority queue | When priorities change |
| PROGRESS.md | Mid-feature checkpoint | During multi-phase work |

---

## Context Reset Triggers

Stop and start fresh when you see:

- "I'll try a different approach"
- Same error 3+ times
- Agent loops without progress
- Context > 60%
- 10+ messages back-and-forth

---

*Print this. Pin it. Use it every session.*
