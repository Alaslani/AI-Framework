# Overview

## What is AI-Framework?

AI-Framework is a methodology for AI-assisted software development. It provides patterns, templates, and workflows that help developers work effectively with AI assistants like Claude, GPT, or Copilot.

## Core Principles

### 1. Clarity Over Speed
AI makes implementation fast. The bottleneck is now knowing what to build. Invest time in research and planning—it pays dividends.

### 2. Spec-First Development

**Specs are the product. Code is the byproduct.**

```
Prompts + Specs = Source code
Generated code  = Compiled artifact
```

The description of what to build matters more than the code itself. Code can be regenerated, but clear specs ensure it's regenerated correctly.

As AI writes more code, **the specs become what you maintain**, not the implementation.

### 3. Signal Over Ceremony
Don't just track status. Capture the **signal**—what you learned, why decisions were made, what didn't work.

### 4. Context Under Control
AI assistants have context limits. Keep context under 40% for better reasoning. Compact when needed, start fresh between unrelated tasks.

> **Note**: 40% is a guideline. Some tasks require more context — use compaction strategies when exceeding.

### 5. Knowledge Compounds
Every session should make the next one better. Capture learnings, update your knowledge base, build institutional memory.

### 6. Verification at Every Gate
Trust but verify. Type check, lint, build, and test after every implementation phase. Prefer deterministic verification (type checkers, compilers, tests) over probabilistic review (LLM self-checks). See [Back Pressure Engineering](methodology/back-pressure.md).

## Why AI Development Breaks

AI development fails after week 2 because:

| Problem | Result |
|---------|--------|
| Context drifts | Same mistakes repeated |
| No explicit handoffs | Knowledge lost between sessions |
| Speed without memory | Technical debt invisible until crisis |
| "Model seemed confident" | Bugs ship to production |
| Learnings evaporate | Team never improves |

This framework exists to make AI development **sustainable**, not just fast.

## Who This Is For

- **Solo developers** building side projects
- **Startups** moving fast with small teams
- **Teams** establishing AI development practices
- **Anyone** wanting to work more effectively with AI

## What This Is NOT

- ❌ A boilerplate or starter template
- ❌ Language-specific (works with any stack)
- ❌ Tool-specific (works with any AI assistant)
- ❌ Prescriptive rules (adapt to your context)

## How to Use This Documentation

1. Start with [Getting Started](getting-started.md) for a 5-minute overview
2. Learn the [FIC Workflow](methodology/fic-workflow.md) for day-to-day work
3. Adopt [Patterns](patterns/session-handoff.md) as you need them
4. Use [Templates](../templates/) as starting points

---

*Next: [Getting Started](getting-started.md)*
