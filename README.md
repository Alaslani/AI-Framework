<p align="center">
  <img src="assets/logo.svg" alt="AI-Framework" width="800">
</p>

<p align="center">
  <strong>Bulletproof AI-Assisted Development</strong><br>
  <em>Stop losing context. Start compounding knowledge.</em>
</p>

<p align="center">
  <a href="CHANGELOG.md"><img src="https://img.shields.io/badge/version-1.6.0-blue" alt="Version"></a>
  <a href="https://github.com/Alaslani/AI-Framework/actions/workflows/lint.yml"><img src="https://github.com/Alaslani/AI-Framework/actions/workflows/lint.yml/badge.svg" alt="Lint"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="MIT License"></a>
  <a href=".github/CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-brightgreen" alt="PRs Welcome"></a>
  <img src="https://img.shields.io/badge/dependencies-zero-orange" alt="Zero Dependencies">
  <a href="https://sahmx.com"><img src="https://img.shields.io/badge/used%20in-SahmX.com-blueviolet" alt="Used in SahmX.com"></a>
</p>

<p align="center">
  <img src="assets/fic-diagram.svg" alt="FIC Workflow: Find → Gate → Implement → Gate → Compound" width="700">
</p>

<p align="center">
  <img src="assets/s2s-diagram.svg" alt="Spec to Signal: Intent → Build → Deploy → Signal → Learn" width="700">
</p>

<p align="center">
  <img src="assets/pack-diagram.svg" alt="PACK System: Project Artifacts for Context Knowledge" width="700">
</p>

---

> **Zero dependencies** — No packages to install, no CLI tools, no configs. Just copy the markdown files and start working.

## Introduction

AI has transformed software development. What once took weeks now takes hours. But this speed creates new challenges: unclear requirements become expensive mistakes faster, context gets lost between sessions, and hard-won learnings disappear when conversations end.

AI-Framework solves these problems with a structured methodology that works for any project, any language, any team size. It focuses on the highest-leverage activities—research and planning—while providing patterns for context management, session continuity, and knowledge preservation.

This is not a boilerplate or template. It's an opinionated guide based on patterns proven across 50+ development sessions. Use what works for you, adapt what doesn't, and stay consistent.

## Quick Start

```bash
# Copy templates to your project
cp -r templates/ /path/to/your/project/
```

1. Fill in `MASTER_REFERENCE.md` with your project details
2. Start every AI session by pointing to your MASTER_REFERENCE
3. Use `TRANSFER_PACK.md` to hand off between sessions
4. Capture learnings after each session — knowledge compounds

> **One-liner setup:** Copy this link and tell your AI: *"Set up AI-Framework for my project"*
> ```
> https://github.com/Alaslani/AI-Framework
> ```

## What makes a project "bulletproof"?

- **Clarity over speed** — Know what to build before building it
- **Signal over ceremony** — Capture learnings, not just status
- **Context under control** — Keep AI context under 40% for better reasoning
- **Knowledge compounds** — Every session makes the next one better
- **Verification at every gate** — Type check, lint, build, test

<details>
<summary><strong>Table of Contents</strong></summary>

- [Overview](docs/overview.md)
- [Getting Started](docs/getting-started.md)
- [Golden Path (Cheat Sheet)](GOLDEN_PATH.md)
- **Methodology**
  - [FIC Workflow](docs/methodology/fic-workflow.md)
  - [PACK System](docs/methodology/pack-system.md)
  - [Phased Development](docs/methodology/phased-development.md)
  - [Validation Gates](docs/methodology/validation-gates.md)
- **Patterns**
  - [Session Handoff](docs/patterns/session-handoff.md)
  - [Project Knowledge](docs/patterns/project-knowledge.md)
  - [Decision Logging](docs/patterns/decision-logging.md)
  - [Subagent Usage](docs/patterns/subagent-usage.md)
  - [Context Warning Signs](docs/patterns/context-warning-signs.md)
  - [Cross-Project Patterns](docs/patterns/cross-project-patterns.md)
  - [Parallel Development](docs/patterns/parallel-development.md)
- **Guides**
  - [Anti-Patterns](docs/anti-patterns.md)
  - [Compound Checklist](docs/compound-checklist.md)
  - [Exit Test](docs/exit-test.md)
  - [FIC Loop Diagram](docs/fic-loop.md)
  - [Framework Contract](FRAMEWORK_CONTRACT.md)
- **Memory**
  - [Memory Setup](docs/memory-setup.md)
  - [AI Memory System](docs/memory/ai-memory-system.md)
  - [Cross-Project Learning](docs/memory/cross-project-learning.md)
- **Integrations**
  - [Notion](docs/integrations/notion.md)
  - [Prompt Templates](docs/integrations/prompt-templates.md)
  - [Claude Code](docs/integrations/claude-code.md) — Plan mode, /rewind, prompt escalation
  - [Other Tools](docs/integrations/other-tools.md) — Cursor, Windsurf, Copilot, Aider
- [Templates](templates/)
- [Examples](examples/) — Filled templates for web, mobile, API projects
- [References](REFERENCES.md) — Methodology sources and credits
- [Security Guide](SECURITY.md) — Vibe coding security checklist
  - [Data Integrity Guide](DATA_INTEGRITY.md) — Ghost columns, write/read mismatches, webhook validation

</details>

## The PACK System

**P**roject **A**rtifacts for **C**ontext **K**nowledge — the 4 documents that preserve context across sessions:

| Document | Purpose | Update Frequency |
|----------|---------|------------------|
| **MASTER_REFERENCE** | Permanent project knowledge (the brain) | After learnings |
| **ROADMAP** | Direction and priorities (the plan) | Weekly or milestone |
| **TRANSFER_PACK** | Session handoff (the memory) | Every session end |
| **PROMPT_TEMPLATES** | Command patterns (the language) | As workflows evolve |

## Core Workflow

| Phase | What You Do | Output |
|-------|-------------|--------|
| **Find** | Understand files, data flow, constraints | Research notes |
| **Implement** | Plan → Execute → Verify (per phase) | Working code |
| **Compound** | Capture learnings | Updated knowledge |

**Context rule:** Keep under 40%. Reset at 60% or after 3 repeated errors.

> See **[Golden Path](GOLDEN_PATH.md)** for the complete workflow cheat sheet.

## Contributing

Contributions welcome! See [CONTRIBUTING.md](.github/CONTRIBUTING.md).

## License

[MIT](LICENSE)
