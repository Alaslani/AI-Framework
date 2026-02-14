<p align="center">
  <img src="assets/logo.svg" alt="AI-Framework" width="800">
</p>

<p align="center">
  <strong>Bulletproof AI-Assisted Development</strong><br>
  <em>Stop losing context. Start compounding knowledge.</em>
</p>

<p align="center">
  <a href="https://github.com/Alaslani/AI-Framework/releases/tag/v2.0.0"><img src="https://img.shields.io/badge/version-2.0.0-blue" alt="Version"></a>
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

> **AI-assisted setup:** Give your AI this repo URL and say *"Set up AI-Framework for my project"* — it knows what to do.

## AI Setup Instructions

> If you're an AI assistant helping someone set up this framework, follow these steps.

### Step 1: Copy Templates

Copy all 4 files from `templates/` into the user's project root:

- `MASTER_REFERENCE.md`
- `TRANSFER_PACK.md`
- `ROADMAP.md`
- `PROMPT_TEMPLATES.md`

### Step 2: Fill In Master Reference

Ask the user about their project and fill in `MASTER_REFERENCE.md`:

- Project name and description
- Repository and production URLs
- Tech stack (frontend, backend, database, hosting)
- Key commands (dev, build, test, deploy)
- Any existing architecture decisions or patterns

### Step 3: Tool-Specific Setup

Ask: "Are you using Claude AI (chat), Claude Code (terminal), or another tool?"

| Tool | Action |
|------|--------|
| **Claude AI** | Walk them through `PROJECT_INSTRUCTIONS.md` — help them fill in the template, set up a Claude Project, and upload PACK docs as Project Knowledge |
| **Claude Code** | Point to `docs/integrations/claude-code.md` — they already have `CLAUDE.md` auto-read. Help set up `.claude/` directory and CLAUDE.md in their project |
| **Cursor** | Point to `docs/integrations/other-tools.md` — help create `.cursorrules` from MASTER_REFERENCE |
| **Windsurf** | Point to `docs/integrations/other-tools.md` — help create `.windsurfrules` from MASTER_REFERENCE |
| **Other** | Point to `docs/integrations/other-tools.md` — adapt the general pattern |

### Step 4: Generate Starting Documents

- Fill in `ROADMAP.md` with their current priorities (ask what they're working on)
- Create their first `TRANSFER_PACK.md` as a starting point
- Set up `docs/memory-setup.md` memory template with their details

### Step 5: Verify Setup

Confirm the user has:

- [ ] 4 PACK documents filled in their project
- [ ] Tool-specific setup complete (Project Instructions, CLAUDE.md, or .cursorrules)
- [ ] Memory configured (if using Claude AI)
- [ ] First session ready to start with `/research` or `/plan`

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
- **Setup**
  - [PROJECT_INSTRUCTIONS.md](PROJECT_INSTRUCTIONS.md) — Claude AI Project setup (copy-paste)
  - CLAUDE.md — Claude Code auto-reads this (no action needed)
- **Methodology**
  - [FIC Workflow](docs/methodology/fic-workflow.md)
  - [PACK System](docs/methodology/pack-system.md)
  - [Phased Development](docs/methodology/phased-development.md)
  - [Validation Gates](docs/methodology/validation-gates.md)
- **Patterns**
  - [Session Handoff](docs/patterns/session-handoff.md)
  - [Project Knowledge](docs/patterns/project-knowledge.md)
  - [Decision Logging](docs/patterns/decision-logging.md)
  - [Subagent Usage](docs/patterns/subagent-usage.md) — Claude Code
  - [Context Warning Signs](docs/patterns/context-warning-signs.md)
  - [Cross-Project Patterns](docs/patterns/cross-project-patterns.md)
- **Guides**
  - [Anti-Patterns](docs/anti-patterns.md) — includes "When NOT to Use"
  - [Memory Setup](docs/memory-setup.md)
- **Integrations**
  - [Claude AI](docs/integrations/claude-ai.md) — Projects, Memory, artifacts
  - [Claude Code](docs/integrations/claude-code.md) — Subagents, /rewind, CLAUDE.md, worktrees
  - [Notion](docs/integrations/notion.md)
  - [Other Tools](docs/integrations/other-tools.md) — Cursor, Windsurf, Copilot, Aider
- [Templates](templates/)
- [Examples](examples/) — Filled templates for web app
- [References](REFERENCES.md) — Methodology sources and credits
- [Security Guide](SECURITY.md) — Vibe coding security checklist
  - [Data Integrity Guide](DATA_INTEGRITY.md) — Ghost columns, write/read mismatches

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

> See **[FIC Workflow](docs/methodology/fic-workflow.md)** for the complete methodology, or **[Getting Started](docs/getting-started.md)** for a quick-start guide.

## Contributing

Contributions welcome! See [CONTRIBUTING.md](.github/CONTRIBUTING.md).

## License

[MIT](LICENSE)
