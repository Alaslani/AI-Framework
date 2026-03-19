# Changelog

All notable changes to AI-Framework.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [2.3.0] - 2026-03-19

### Added
- `docs/methodology/evals-system.md` — 3-layer automated quality enforcement (ESLint type-checked rules → Claude Code hooks → cross-model review)
- `docs/diagrams/` — 8 Mermaid diagrams for FIC workflow, evals pyramid, back pressure, context engineering, agent selection, session lifecycle, hook execution, and dual verification
- Evals System and Diagrams added to README table of contents

### Attribution
- Proven across two production codebases (50+ Claude Code sessions)
- Claude Code hooks system (Anthropic Claude Code team)
- Cross-model verification pattern (independent AI reviewer catches what the implementing model misses)

---

## [2.2.0] - 2026-02-28

### Added
- **Progressive Disclosure** pattern in `docs/integrations/claude-code.md` — keep CLAUDE.md lean, load details on demand via skill files
- **Periodic Tool Audit** methodology in `docs/integrations/claude-code.md` — audit commands, agents, skills for overlap every 20-30 sessions
- **Slot Machine** (autonomous mode) pattern in `docs/integrations/claude-code.md` — checkpoint → auto work → binary accept/revert
- **Session End Protocol** in `docs/integrations/claude-code.md` — replaces /compound with CLAUDE.md self-improvement loop
- **Parallel Exploration** as 4th prompt escalation pattern
- 3 new anti-patterns: System Prompt Bloat (#12), Tool Proliferation Without Audit (#13), Constraining Language (#14)
- `/auto` and `/session-end` commands in `templates/PROMPT_TEMPLATES.md`

### Changed
- `/compound` replaced by `/session-end` (includes compound + CLAUDE.md update)
- Removed "Thinking Keywords" from prompt templates (models handle this natively — constraining language lesson)
- `templates/PROMPT_TEMPLATES.md` version bumped to v2.2

### Attribution
- Thariq (@trq212), Anthropic Claude Code team — "Lessons from Building Claude Code: Seeing like an Agent" (Feb 27, 2026)
- Anthropic internal teams (Data Science, RL Engineering, Data Infrastructure, Product Design) — workflow patterns

---

## [2.1.1] - 2026-02-17

### Added
- **Visual Architecture Diagrams** as back pressure technique in `docs/methodology/back-pressure.md`
- Implementation approaches for JS/TS, Python, Rust, and Go dependency visualization
- Decision table for when to invest in architecture diagram generation

### Attribution
- Boundary/BAML team's CargoStow tool concept (AI That Works podcast)

---

## [2.1.0] - 2026-02-17

### Added
- `docs/methodology/learning-tests.md` — `/learn` command for proving external system behavior before planning
- `docs/methodology/back-pressure.md` — Deterministic feedback loops for AI coding agents (pyramid model)
- `/learn` step integrated into FIC workflow between Find and Implement
- Learning Gate added to validation gates (pre-planning check for external integrations)
- Back pressure specification required for every `/implement` phase
- `templates/LEARNING_TESTS.md` — Template for documenting learning test results
- Three new anti-patterns: black-box without /learn, LLM self-review as verification, back pressure after code

### Changed
- FIC workflow: Find → Learn (if external) → Implement → Compound
- Validation gates: back pressure requirement per phase, learning gate for external integrations
- `templates/PROMPT_TEMPLATES.md`: `/learn` command, back pressure in plans (v2.0 → v2.1)
- README: two new "bulletproof" criteria, updated TOC

### Attribution
- Michael Feathers, "Working Effectively with Legacy Code" — learning test concept origin
- AI That Works podcast — "Learning Tests" episode (Dexter Horthy & Vaibhav/BAML)
- Jeff Huntley — deterministic feedback loop diagram

---

## [2.0.0] - 2026-02-14

### Added

- `CLAUDE.md` — Auto-read by Claude Code, routes agent to correct files
- `PROJECT_INSTRUCTIONS.md` — Ready-to-paste template for Claude AI Projects
- `docs/integrations/claude-ai.md` — Dedicated Claude AI guide
  (Projects, Memory, session workflow, artifacts, Claude AI + Code combo)
- Subagent usage doc now has Claude Code header note

### Changed

- BREAKING: Core methodology is now tool-agnostic
  (Claude-specific features live in integration guides only)
- `@[agent]` syntax removed from all prompt templates — commands are universal
- "Agent Selection" renamed to "Task Focus" — mental model, not a feature
- Subagent pattern moved from core methodology to Claude Code integration
- Recovery pattern is universal — tool-specific variants in integration guides
- Parallel development (worktrees) moved from core to Claude Code integration
- Memory docs consolidated into single `docs/memory-setup.md`

### Removed

- `GOLDEN_PATH.md` (consolidated into README and methodology)
- `FRAMEWORK_CONTRACT.md` (rules integrated into methodology docs)
- `docs/fic-loop.md` (ASCII diagram merged into fic-workflow.md)
- `docs/compound-checklist.md` (merged into fic-workflow.md Compound section)
- `docs/exit-test.md` (merged into anti-patterns.md as "When NOT to Use")
- `docs/integrations/prompt-templates.md` (duplicate of templates/)
- `docs/memory/cross-project-learning.md` (generic advice)
- `docs/memory/ai-memory-system.md` (merged into memory-setup.md)
- `docs/patterns/parallel-development.md` (moved to claude-code.md)
- `examples/mobile-app/` and `examples/api-backend/` (one example sufficient)

### Improved

- ~45% content reduction, zero duplicate explanations
- Each concept explained in exactly one place
- Claude AI and Claude Code users each have a dedicated guide
- New projects get instant setup via CLAUDE.md or PROJECT_INSTRUCTIONS.md

---

## [1.6.0] - 2026-02-12

### Added
- `DATA_INTEGRITY.md` — Data integrity guide for AI-assisted development
  - Ghost Column detection pattern (BaaS clients silently return undefined for non-existent columns)
  - Write/Read Path Mismatch tracing methodology (critical path documentation)
  - Silent Callback/Webhook failure prevention (schema-validated integration tests)
  - Prevention checklist, AI audit prompts, and common mistakes table
- Data Integrity Guide added to README table of contents

### Context
- Born from discovering 67 silent ghost columns in a production codebase
- BaaS platforms (Supabase, Firebase, Hasura) don't validate column names at runtime
- Traditional testing catches logic errors but misses column name mismatches

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
