# Session Lifecycle

The full lifecycle of an AI-assisted development session, from loading context to preserving knowledge.

```mermaid
flowchart LR
    START([Session Start]) --> LOAD[Load PACK\nMaster Reference\nTransfer Pack\nRoadmap]

    LOAD --> FIC

    subgraph FIC [FIC Loop]
        direction TB
        F[Find\nResearch + Learn] --> I[Implement\nPlan + Execute + Verify]
        I --> C[Compound\nCapture Learnings]
        C -->|More work?| F
    end

    FIC --> END_SESSION[Session End\nUpdate PACK docs]
    END_SESSION --> TP([Transfer Pack\nHandoff to next session])

    style START fill:#4a9eff,color:#fff
    style LOAD fill:#4a9eff,color:#fff
    style F fill:#4a9eff,color:#fff
    style I fill:#7ed321,color:#fff
    style C fill:#9b59b6,color:#fff
    style END_SESSION fill:#f5a623,color:#fff
    style TP fill:#27ae60,color:#fff
```

**Key points:**
- **Start:** Always load existing PACK documents for continuity
- **FIC Loop:** Repeats within a session — multiple find-implement-compound cycles are normal
- **End:** Capture learnings and create a Transfer Pack so the next session starts ahead

**When to use:** Onboarding someone to the AI-Framework workflow, or explaining why session start and end rituals matter for knowledge compounding.

*See: [Session Handoff](../patterns/session-handoff.md), [FIC Workflow](../methodology/fic-workflow.md)*
