# Context Engineering

How AI context budget is allocated across a session, and what triggers a fresh context.

```mermaid
flowchart TD
    subgraph Budget["Context Budget"]
        PC["Project Context ~15%"]
        CV["Conversation ~25%"]
        TR["Tool Results ~20%"]
        RS["Reserved ~40%"]
    end

    Budget --> CHECK{Usage Level?}
    CHECK -->|Under 40%| GOOD([Optimal])
    CHECK -->|40-60%| WARN([Caution])
    CHECK -->|Over 60%| RESET([Reset])

    style PC fill:#4a9eff,color:#fff
    style CV fill:#9b59b6,color:#fff
    style TR fill:#f5a623,color:#fff
    style RS fill:#27ae60,color:#fff
    style GOOD fill:#27ae60,color:#fff
    style WARN fill:#f5a623,color:#fff
    style RESET fill:#e74c3c,color:#fff
```

**Fresh context triggers:**
- Context usage exceeds 60%
- 3 or more repeated errors in a row
- AI starts producing inconsistent or contradictory output
- Switching to a fundamentally different task

**When to use:** Explaining context management to users hitting quality degradation, or planning how much project context to load at session start.

*See: [Context Warning Signs](../patterns/context-warning-signs.md)*
