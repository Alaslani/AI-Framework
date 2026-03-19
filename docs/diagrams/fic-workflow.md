# FIC Workflow

The Find → Implement → Compound development loop with validation gates between each phase.

```mermaid
flowchart LR
    R[Research] --> UG{Understanding\nGate}
    UG -->|Pass| EXT{External\nIntegration?}
    UG -->|Fail| R

    EXT -->|Yes| LT[Learning\nTests]
    EXT -->|No| P[Plan]

    LT --> LG{Learning\nGate}
    LG -->|All ✅| P
    LG -->|Any ❌| LT

    P --> AG{Approach\nGate}
    AG -->|Pass| I[Implement]
    AG -->|Fail| P

    I --> IG{Implementation\nGate}
    IG -->|Pass| C[Compound]
    IG -->|Fail| I

    C --> DONE([Session End\nTransfer Pack])

    style R fill:#4a9eff,color:#fff
    style LT fill:#4a9eff,color:#fff
    style P fill:#f5a623,color:#fff
    style I fill:#7ed321,color:#fff
    style C fill:#9b59b6,color:#fff
```

**When to use:** Explaining the core FIC methodology to new users, or as a quick reference for the development loop and where gates sit between phases.

*See: [FIC Workflow](../methodology/fic-workflow.md)*
