# 3-Layer Evals Pyramid

Automated quality enforcement layers, ordered from most deterministic (bottom) to least deterministic (top).

```mermaid
flowchart TB
    L3["Layer 3: Cross-Model Review\nDesign, architecture, blind spots"]
    L2["Layer 2: Claude Code Hooks\nTaste, rationalization, domain rules"]
    L1["Layer 1: ESLint Type-Checked Rules\nTypes, async, promises, any-leakage"]

    L3 --> L2 --> L1

    style L3 fill:#e74c3c,color:#fff
    style L2 fill:#f5a623,color:#fff
    style L1 fill:#27ae60,color:#fff
```

| Layer | What It Catches | Determinism |
|-------|----------------|-------------|
| **Layer 1:** ESLint | Types, async, promises, any-leakage | Highest — same code, same result |
| **Layer 2:** Hooks | Taste, rationalization, domain anti-patterns | High — shell scripts, deterministic |
| **Layer 3:** Cross-Model | Design, architecture, blind spots | Lower — different model, different perspective |

**When to use:** Introducing the evals system to someone unfamiliar with it, or deciding which layer to invest in next.

*See: [Evals System](../methodology/evals-system.md)*
