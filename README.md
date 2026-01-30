# AI-Framework

A lightweight, language-agnostic methodology for AI-assisted software development.

## Table of Contents

- [Introduction](#introduction)
- [Quick Start](#quick-start)
- [The FIC Workflow](#the-fic-workflow)
- [Documentation](#documentation)
- [Templates](#templates)
- [Adoption Path](#adoption-path)
- [Contributing](#contributing)
- [License](#license)

## Introduction

AI-Framework provides a structured approach to working with AI coding assistants. It's built on one core insight:

> **AI collapsed the cost of implementation. Clarity is now the bottleneck.**

This framework helps you spend time on the right problems—understanding and planning—while letting AI handle execution.

### Key Principles

- **Research before acting** — Understand code before changing it
- **Plan at the right level** — Spec → Tech → Steps
- **Validate continuously** — Check after each phase
- **Capture learnings** — Build knowledge that compounds

### What This Is NOT

- ❌ A tool or CLI (zero dependencies)
- ❌ Language-specific (works with any stack)
- ❌ All-or-nothing (use what you need)
- ❌ AI-tool-specific (works with any assistant)

## Quick Start

**Option 1: Minimal (2 minutes)**

```bash
# Copy the project context template
curl -o CLAUDE.md https://raw.githubusercontent.com/Alaslani/AI-Framework/main/templates/CLAUDE.md.template

# Edit for your project
```

**Option 2: Full Workflow (5 minutes)**

```bash
# Clone the repo
git clone https://github.com/Alaslani/AI-Framework.git

# Copy templates to your project
cp AI-Framework/templates/* your-project/docs/
```

## The FIC Workflow

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   FIND              IMPLEMENT           COMPOUND        │
│   ────              ─────────           ────────        │
│   Research          Execute phases      Capture         │
│   Plan approach     Validate each       learnings       │
│   Define success    Keep context lean   Update docs     │
│                                                         │
│   [~20% time]       [~70% time]         [~10% time]     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Phase Overview

| Phase | Input | Output | Key Question |
|-------|-------|--------|--------------|
| **Research** | Task/bug | Understanding | "What exists?" |
| **Planning** | Research | Approach | "How to build it?" |
| **Implementation** | Plan | Working code | "Does it work?" |
| **Review** | Code | Validated code | "Is it correct?" |
| **Retrospective** | Experience | Learnings | "What did we learn?" |

## Documentation

| Document | Purpose |
|----------|---------|
| [Philosophy](docs/00-philosophy.md) | Core principles and mental models |
| [When to Use AI](docs/01-when-to-use-ai.md) | Decision framework |
| [Research Phase](docs/02-research-phase.md) | Understanding before acting |
| [Planning Phase](docs/03-planning-phase.md) | Designing the approach |
| [Implementation](docs/04-implementation-phase.md) | Executing with validation |
| [Review Phase](docs/05-review-phase.md) | Quality gates |
| [Retrospective](docs/06-retrospective-phase.md) | Learning capture |
| [Context Management](docs/07-context-management.md) | Keeping AI productive |
| [Skills](docs/08-skills.md) | Composable capabilities |
| [Scaling](docs/09-scaling.md) | Solo → Team → Enterprise |
| [Quick Reference](docs/10-quick-reference.md) | Cheat sheet |

## Templates

Ready-to-use templates for common workflows:

| Template | Purpose | When to Use |
|----------|---------|-------------|
| [CLAUDE.md](templates/CLAUDE.md.template) | Project context | Every project |
| [Feature Plan](templates/feature-plan.md) | Spec + Tech + Steps | New features |
| [Session Handoff](templates/session-handoff.md) | Context transfer | Between sessions |
| [Retrospective](templates/retrospective.md) | Learning capture | After completing work |

## Adoption Path

### Week 1: Foundation
- [ ] Add `CLAUDE.md` to your project
- [ ] Practice "understand before changing"
- [ ] Verify every AI-generated change

### Week 2: Structure
- [ ] Use Research → Plan → Implement flow
- [ ] Keep context under 40%
- [ ] Clear context between tasks

### Week 3: Capture
- [ ] Document learnings after tasks
- [ ] Start pattern collection
- [ ] Log significant decisions

### Week 4+: Optimize
- [ ] Review and refine workflow
- [ ] Share patterns with team
- [ ] Customize templates

## Contributing

See [CONTRIBUTING.md](.github/CONTRIBUTING.md) for guidelines.

We welcome:
- Documentation improvements
- New patterns from real usage
- Template refinements
- Translations

## License

[MIT](LICENSE)

---

<div align="center">

**[Documentation](docs/)** · **[Templates](templates/)** · **[Contributing](.github/CONTRIBUTING.md)**

</div>
