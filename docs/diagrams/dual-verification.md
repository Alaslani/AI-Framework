# Dual Verification Model

How implementing and reviewing models complement each other in the PR lifecycle.

```mermaid
flowchart TD
    DEV[Claude Code] --> PR([PR Created])
    PR --> AUTO[Auto Review]
    AUTO --> FIX1[Fix Findings]
    FIX1 --> HUMAN{Human Review}
    HUMAN -->|Small change| MERGE([Merge])
    HUMAN -->|Significant| MANUAL[Manual Review]
    MANUAL --> FIX2[Fix Findings]
    FIX2 --> MERGE

    style DEV fill:#4a9eff,color:#fff
    style AUTO fill:#f5a623,color:#fff
    style MANUAL fill:#e74c3c,color:#fff
    style HUMAN fill:#9b59b6,color:#fff
    style MERGE fill:#27ae60,color:#fff
```

**Key finding:** Auto and manual reviews find different issues — they stack, they don't overlap.

| Review Type | Catches | Cost |
|-------------|---------|------|
| **Auto review** | Style violations, obvious bugs, type issues | Low — runs on every PR |
| **Manual review** | Design flaws, missing edge cases, architecture drift | Higher — reserve for significant changes |
| **Combined** | Both layers together | Highest value — different blind spots |

**When to use:** Setting up a cross-model review workflow, or explaining why auto-review alone isn't sufficient for architecture changes.

*See: [Evals System](../methodology/evals-system.md)*
