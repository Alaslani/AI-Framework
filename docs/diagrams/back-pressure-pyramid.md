# Back Pressure Pyramid

The verification hierarchy from most deterministic (top) to least deterministic (bottom). Higher layers produce more trustworthy signals.

```mermaid
flowchart TB
    TC[Type Checker]
    B[Build]
    UT[Unit Tests]
    IT[Integration Tests]
    LT[Learning Tests]
    VM[Visual / Manual]
    LLM[LLM Review]

    TC --> B --> UT --> IT --> LT --> VM --> LLM

    style TC fill:#27ae60,color:#fff
    style B fill:#27ae60,color:#fff
    style UT fill:#2ecc71,color:#fff
    style IT fill:#f1c40f,color:#000
    style LT fill:#f5a623,color:#fff
    style VM fill:#e67e22,color:#fff
    style LLM fill:#e74c3c,color:#fff
```

| Signal | Reliability |
|--------|-------------|
| Green | Deterministic — pass or fail, no interpretation |
| Yellow | Semi-deterministic — depends on test quality |
| Red | Probabilistic — can be steered by prompt |

**When to use:** Explaining why type checkers matter more than LLM self-review, or auditing which verification layers a project has in place.

*See: [Back Pressure Engineering](../methodology/back-pressure.md)*
