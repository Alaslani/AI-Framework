# Diagrams

Visual references for AI-Framework concepts. All diagrams use [Mermaid](https://mermaid.js.org/) syntax, which GitHub renders natively — no plugins or build steps required.

## Index

| Diagram | File | When to Use |
|---------|------|-------------|
| FIC Workflow | [fic-workflow.md](fic-workflow.md) | Explaining the core Find → Implement → Compound loop |
| Evals Pyramid | [evals-pyramid.md](evals-pyramid.md) | Introducing the 3-layer quality enforcement system |
| Back Pressure Pyramid | [back-pressure-pyramid.md](back-pressure-pyramid.md) | Explaining the verification hierarchy |
| Context Engineering | [context-engineering.md](context-engineering.md) | Context budget and when to reset |
| Agent Selection | [agent-selection.md](agent-selection.md) | Choosing the right approach for a task |
| Session Lifecycle | [session-lifecycle.md](session-lifecycle.md) | Understanding the full session flow |
| Hook Execution Flow | [hook-execution-flow.md](hook-execution-flow.md) | How Claude Code hooks gate tool execution |
| Dual Verification | [dual-verification.md](dual-verification.md) | Cross-model review in the PR lifecycle |

## Usage

Each file is self-contained with:
- A title and brief description
- A Mermaid diagram (renders automatically on GitHub)
- A "When to use" note
- Links to the related methodology or pattern doc

These diagrams complement the ASCII diagrams in the methodology docs. Use them in presentations, onboarding, or whenever a visual explanation helps.
