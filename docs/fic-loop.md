# FIC Loop

The core workflow visualized.

---

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

---

## Key Points

1. **Gates are mandatory** — Don't skip verification
2. **Phases are sequential** — Don't parallelize Find and Implement
3. **Compound closes the loop** — Knowledge feeds future work
4. **Context resets between tasks** — Fresh start with handoff docs

---

*One diagram. Memorize it.*
