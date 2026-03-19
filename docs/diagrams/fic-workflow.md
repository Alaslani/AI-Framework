# FIC Workflow

The Find → Implement → Compound development loop with validation gates between each phase.

```mermaid
flowchart TD
    R[Research] --> UG{Understanding Gate}
    UG -->|Pass| P[Plan]
    UG -->|Fail| R
    UG -->|External?| LT[Learning Tests]
    LT -->|Proven| P
    LT -->|Wrong| LT
    P --> AG{Approach Gate}
    AG -->|Pass| I[Implement]
    AG -->|Fail| P
    I --> IG{Implementation Gate}
    IG -->|Pass| C[Compound]
    IG -->|Fail| I
    C --> DONE([Transfer Pack])

    style R fill:#4a9eff,color:#fff
    style LT fill:#4a9eff,color:#fff
    style P fill:#f5a623,color:#fff
    style I fill:#7ed321,color:#fff
    style C fill:#9b59b6,color:#fff
```

**When to use:** Explaining the core FIC methodology to new users, or as a quick reference for the development loop and where gates sit between phases.

*See: [FIC Workflow](../methodology/fic-workflow.md)*
