# AI-Framework

This repo is a methodology for AI-assisted development, not a codebase.
Do NOT try to run, build, or test it — it is pure markdown.

## What This Is

A structured methodology (FIC + PACK) for working with AI assistants.
Templates, guides, and patterns for context management, session handoff,
and knowledge preservation.

## Reading Order

1. `templates/MASTER_REFERENCE.md` — project knowledge base template
2. `templates/PROMPT_TEMPLATES.md` — commands and workflows
3. `templates/TRANSFER_PACK.md` — session handoff format
4. `templates/ROADMAP.md` — priority tracking format

## Core Concepts

- **FIC**: Find → Implement → Compound (the workflow)
- **PACK**: Project Artifacts for Context Knowledge (the 4 documents)
- **40% Rule**: Keep context under 40% for best reasoning
- **Verification**: Type check → Lint → Build → Test after every phase
- **Signal over Status**: Capture what you learned, not just what you did

## When Someone Says "Set up AI-Framework for my project"

1. Copy `templates/` to their project
2. Help fill in MASTER_REFERENCE with their project details
3. Point them to the right integration guide:
   - Claude Code users → `docs/integrations/claude-code.md`
   - Claude AI (chat) users → `docs/integrations/claude-ai.md`
   - Cursor/Windsurf/other → `docs/integrations/other-tools.md`

## Claude Code-Specific Guide

See `docs/integrations/claude-code.md` for:

- CLAUDE.md investment pattern
- Plan mode (Shift+Tab)
- /rewind for recovery
- Subagent patterns
- Git worktrees for parallel work
- Permission hooks

## Do NOT

- Treat templates as final docs — they have [placeholders] to fill in
- Modify docs/ unless explicitly asked
- Run any code — this repo has zero dependencies
- Confuse this repo's CLAUDE.md with a project's CLAUDE.md
  (this one describes the framework repo; projects create their own)
