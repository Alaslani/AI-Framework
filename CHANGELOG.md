# Changelog

All notable changes to AI-Framework.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.5.0] - 2026-02-08

### Added
- `docs/patterns/parallel-development.md` — Git worktrees for parallel AI sessions
- `docs/integrations/claude-code.md` — Dedicated Claude Code integration guide
  - Plan mode, /rewind, CLAUDE.md investment, skills/commands
  - Subagent patterns, permission hooks, prompt escalation
  - /statusline, context management thresholds
- Prompt Escalation Patterns in PROMPT_TEMPLATES (Elegant Solution, Grill, Prove It Works)

### Changed
- `docs/patterns/context-warning-signs.md` — Added /rewind as Step 2 in Recovery Pattern (before "start fresh")
- `templates/PROMPT_TEMPLATES.md` — New Section 8 (Prompt Escalation), version bumped to 1.2

### Attribution
- Boris Cherny (@bcherny), Anthropic Claude Code team — "10 Tips for Claude Code" thread (Feb 1, 2026)

---

## [1.4.0] - 2026-01-30

### Added
- `assets/fic-diagram.svg` — FIC workflow visual diagram
- `assets/s2s-diagram.svg` — Spec to Signal (S2S) workflow diagram
- `docs/anti-patterns.md` — 8 common framework misuses
- `docs/compound-checklist.md` — End-of-session questions
- `docs/exit-test.md` — When NOT to use framework
- `docs/fic-loop.md` — ASCII workflow diagram
- "Guides" section in README table of contents
- "Why AI Development Breaks" section in overview.md
- "Severity Levels" table in fic-workflow.md

### Changed
- S2S corrected from "Signal-to-Status" to "Spec to Signal"
- Full S2S flow documented: Spec → Build → Deploy → Signal → Learn
- Added clarification distinguishing "Signal over Status" principle from S2S methodology

### Fixed
- REFERENCES.md: Correct S2S definition
- PROMPT_TEMPLATES.md: Correct S2S definition
- decision-logging.md: Clarification note added

---

## [1.3.2] - 2026-01-30

### Added
- `docs/anti-patterns.md` — 8 common framework misuses to avoid
- `docs/compound-checklist.md` — End-of-session questions
- `docs/exit-test.md` — When NOT to use the framework
- `docs/fic-loop.md` — ASCII workflow diagram
- `assets/fic-diagram.svg` — Clean SVG workflow diagram
- FIC diagram added to README header
- "Guides" section in README table of contents
- "Why AI Development Breaks" section in overview.md
- "Severity Levels" table in fic-workflow.md

---

## [1.3.1] - 2026-01-30

### Added
- `docs/golden-path.md` — Single-page cheat sheet for daily use
  - Session start/end procedures
  - Workflow selection guide
  - Context management rules
  - Thinking keywords reference
  - Context reset triggers

---

## [1.3.0] - 2026-01-30

### Changed
- Moved `SECURITY.md` to repository root for visibility
- Fixed FIC terminology: "Find →" replaces "Research →" in workflow contexts
- Added 40% context rule heuristic notes (guideline, not hard limit)

### Added
- `templates/README.md` — Quick start for copying templates
- Session Scripts section in `docs/getting-started.md`
- "When to Reset Context" guidance
- CI status badge in README

### Removed
- Internal files: `AI-FRAMEWORK-AUDIT.md`, `research/` folder

---

## [1.2.2] - 2026-01-30

### Added
- Expanded security guide with 7 additional issues:
  - Rate limiting
  - Verbose error messages
  - CORS misconfiguration
  - Missing security headers
  - Dependency vulnerabilities
  - CSRF protection
  - Hardcoded admin accounts
- Extended security checklist with 4 new categories

---

## [1.2.1] - 2026-01-30

### Added
- `SECURITY.md` — Security guide for AI-assisted development (moved to root)
  - Covers exposed API keys, missing authorization, unvalidated input
  - Includes AI audit prompts and pre-deploy checklists

---

## [1.2.0] - 2026-01-30

### Added
- `examples/` folder with three project types (web-app, mobile-app, api-backend)
- Filled example templates showing real-world usage
- `docs/patterns/cross-project-patterns.md` with transferable learnings
- `docs/integrations/other-tools.md` for Cursor, Windsurf, Copilot, Aider, Cline
- GitHub issue templates (bug, feature, pattern)
- CHANGELOG.md for version tracking
- README badges

### Changed
- Added examples to README table of contents

---

## [1.1.0] - 2026-01-30

### Added
- Context engineering patterns from Dexter Horthy
- `docs/patterns/subagent-usage.md` — Context control with spawned agents
- `docs/patterns/context-warning-signs.md` — When to start fresh
- Proper FIC attribution in REFERENCES.md
- PROMPT_TEMPLATES v1.1 with:
  - Section 0: Context Engineering Principles
  - Research output format with line numbers
  - Common mistakes table
  - References section

### Changed
- Enhanced `docs/methodology/fic-workflow.md`:
  - Added research output format
  - Added intentional compaction section
- Enhanced `docs/methodology/validation-gates.md`:
  - Added human review emphasis
  - Added review checklists
- Enhanced `docs/overview.md`:
  - Added spec-first development principle
  - Now has 6 core principles (was 5)
- Enhanced `docs/methodology/phased-development.md`:
  - Added "Plans as Alignment Tools" section

---

## [1.0.0] - 2026-01-30

### Added
- Initial release of AI-Framework
- Core FIC methodology (Find → Implement → Compound)
- Documentation structure:
  - `docs/overview.md` — Core principles
  - `docs/getting-started.md` — Quick start guide
  - `docs/methodology/` — FIC workflow, phased development, validation gates
  - `docs/patterns/` — Session handoff, project knowledge, decision logging
  - `docs/memory/` — AI memory system, cross-project learning
  - `docs/integrations/` — Notion, prompt templates
- Templates:
  - `MASTER_REFERENCE.md` — Project knowledge base
  - `TRANSFER_PACK.md` — Session handoff
  - `ROADMAP.md` — Priority tracking
  - `PROMPT_TEMPLATES.md` — Command reference
- `REFERENCES.md` — Methodology sources and credits
- `.github/CONTRIBUTING.md` — Contribution guidelines
- MIT License

---

## Future Plans

- [ ] Video tutorials
- [ ] Translations (Spanish, Arabic, Chinese)
- [ ] Interactive documentation site
- [ ] VS Code extension for templates

---

*For detailed changes, see [commit history](https://github.com/Alaslani/AI-Framework/commits/main).*
