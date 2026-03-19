# Agent Selection Flowchart

Decision tree for choosing the right approach when working with Claude Code.

```mermaid
flowchart TD
    START([New Task]) --> Q1{Quick task?}
    Q1 -->|Yes| DIRECT[Direct Execution]
    Q1 -->|No| Q2{Need research?}
    Q2 -->|Yes| EXPLORE[Explore First]
    Q2 -->|No| Q3{Architecture decisions?}
    EXPLORE --> Q3
    Q3 -->|Yes| PLAN[Plan First]
    Q3 -->|No| Q4{Independent subtasks?}
    PLAN --> Q4
    Q4 -->|Yes| PARALLEL[Parallel Work]
    Q4 -->|No| SEQUENTIAL[Sequential Phases]

    style START fill:#4a9eff,color:#fff
    style DIRECT fill:#27ae60,color:#fff
    style EXPLORE fill:#4a9eff,color:#fff
    style PLAN fill:#f5a623,color:#fff
    style PARALLEL fill:#9b59b6,color:#fff
    style SEQUENTIAL fill:#9b59b6,color:#fff
```

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Direct execution | < 20 lines, one file, obvious change | Fix a typo, add an import |
| Explore first | Unfamiliar code, need to trace data flow | Debug a cross-module issue |
| Plan first | Multiple files, architectural decisions | Add a new feature with API + UI |
| Parallel work | Independent tasks, no shared state | Lint fix + test addition + docs update |
| Sequential phases | Ordered dependencies between steps | Database migration then API then UI |

**When to use:** Deciding how to approach a new task, or explaining to someone why certain tasks need research before implementation.

*See: [Subagent Usage](../patterns/subagent-usage.md), [Phased Development](../methodology/phased-development.md)*
