# Evals System — Implementation Plan

Add automated quality enforcement (evals) to AI-Framework as general methodology for Claude Code users. Three phases, each independently shippable.

---

## Phase 1: Core Documentation

**Goal:** Create `docs/methodology/evals-system.md` — the 3-layer evals system as general methodology.

### What to write

A single document (~350-400 lines) covering the complete evals system. Structure follows existing methodology doc conventions (H1 title, subtitle, intro, ASCII diagram, core sections, tables, footer).

**Opening (lines 1-30):**
- Title: "Evals System"
- Subtitle: "Automated quality enforcement for AI-assisted development"
- Problem statement: back pressure pyramid tells you *what* to verify, evals tells you *how* to enforce it automatically. This is the enforcement layer that catches errors without human review.
- Connection to back-pressure.md philosophy: deterministic > probabilistic
- ASCII diagram: 3-layer pyramid (ESLint base → CC Hooks middle → Cross-Model Review top)

**Layer 1 — ESLint Type-Checked Rules (lines 31-130):**

Why this is the foundation:
- Lint is deterministic — same code always gets same result (ties to back-pressure.md pyramid principle)
- Runs in milliseconds, catches entire categories of bugs
- TypeScript-specific: type-checked rules catch async bugs, any-leakage, promise misuse that plain ESLint misses

Setup walkthrough (generic, not project-specific):
- ESLint 9+ flat config with `typescript-eslint` `recommendedTypeChecked`
- `tsconfig.eslint.json` pattern for monorepos (one per package, extends base)
- Key rules table:

| Rule | What It Catches | Why It Matters |
|------|----------------|----------------|
| `no-floating-promises` | Unhandled promise rejections | Silent failures in production |
| `no-misused-promises` | Promises in boolean context | Conditionals that always pass |
| `await-thenable` | Awaiting non-promises | Misleading code, hides bugs |
| `no-explicit-any` | Type safety escape hatches | Undermines entire type system |

Calibration workflow (the non-obvious part):
1. Run lint on full codebase — get actual violation counts
2. Key lesson: **grep estimates are 5x off** — always run actual lint, never guess
3. Rules with zero violations → set to `"error"` immediately (free wins)
4. Rules with high violations → set to `"warn"`, fix in batches, promote to `"error"` when count hits zero
5. Track violation count per rule over time

Anti-patterns:
- Starting with all rules as `"error"` → 500 failures → disabled entirely
- Keeping rules at `"warn"` forever → warnings become wallpaper
- Skipping type-checked rules → misses the highest-value catches

**Layer 2 — Claude Code Hooks (lines 131-260):**

Why hooks exist in the evals stack:
- Lint catches syntax/type violations. Hooks catch *taste* violations — rationalization, domain-specific anti-patterns, things that are technically valid code but wrong for your project
- Hooks are `type: "command"` shell scripts — deterministic, auditable, versionable

Three hook types with shell script templates:

**PreToolUse (gates)** — runs before tool execution, can block:
```bash
#!/bin/bash
# Example: block writes to protected paths
# Hook receives tool name and parameters on stdin
```
- Use case: prevent writes to migration files without review, block certain file patterns
- Must exit 0 to allow, non-zero to block
- Keep fast — this blocks every tool call

**PostToolUse (advisories)** — runs after tool execution, advises:
```bash
#!/bin/bash
# Example: warn when file exceeds line count threshold
# Hook receives tool name, parameters, and result on stdin
```
- Use case: flag files growing too large, warn about import patterns
- Output shown to Claude as advisory — doesn't block
- Good for soft rules that need judgment

**Stop (self-review)** — runs when Claude thinks it's done:
```bash
#!/bin/bash
# Example: check diff for anti-patterns before completing
# CRITICAL: diff HEAD (uncommitted changes vs last commit)
```
- Must diff `HEAD` — shows uncommitted changes against the last commit
- Anti-rationalization hook: grep diff output for phrases like "for now", "we can fix later", "workaround" — these signal Claude is making excuses instead of solving the problem
- Domain-specific taste rules: each project defines what "wrong but valid" looks like

Critical implementation details:
- ALL hooks must be `type: "command"` — never `"prompt"` or `"agent"` (those aren't deterministic)
- Hooks live in `.claude/settings.json` or `.claude/settings.local.json`
- Shell scripts should be in a `.claude/hooks/` directory, committed to repo
- Keep hooks fast (<2s) — they run on every tool call
- Test hooks manually before trusting them

Anti-patterns:
- Using `type: "prompt"` — makes the hook probabilistic, defeating the purpose
- Stop hook that diffs the wrong target (e.g., `git show` instead of `git diff HEAD`)
- Hooks that are too slow (>5s) — developers disable them
- Too many PreToolUse hooks — blocks workflow

**Layer 3 — Cross-Model Review (lines 261-330):**

Why different models catch different things:
- The model that wrote the code has blind spots about its own output
- A second model (different architecture, different training) catches what the first missed
- This is the least deterministic layer but catches the highest-level issues (design, architecture, missed requirements)

Setup:
- `AGENTS.md` at repo root with blocking + non-blocking rules
- Blocking rules: things that must be fixed before merge (security, type errors, test failures)
- Non-blocking rules: style, naming, documentation suggestions
- Auto-review triggers on PR open — catches N issues
- Manual deep review for significant changes — catches M additional issues
- Key finding: auto and manual reviews stack — they find different things

When to use each review type:

| Change Type | Auto Review | Manual Review |
|-------------|------------|---------------|
| Bug fix (< 50 lines) | ✅ Sufficient | Skip |
| Feature (50-200 lines) | ✅ First pass | ✅ If touching core paths |
| Architecture change | ✅ First pass | ✅ Required |
| Dependency update | ✅ Sufficient | Skip |

Anti-patterns:
- Relying on cross-model review alone (skipping layers 1-2)
- Treating all findings as blocking — review fatigue
- Not having an `AGENTS.md` — reviewer has no project context

**Implementation Playbook (lines 331-380):**

Phase sequence — each layer builds on the previous:
1. **ESLint first** — get deterministic baseline (1-2 days for greenfield, 1-2 weeks for migration)
2. **CC Hooks** — add taste enforcement after lint is clean (1 day setup, iterate over time)
3. **Cross-model review** — add after hooks catch the obvious stuff (1 hour setup)

Greenfield vs migration:

| Scenario | ESLint | Hooks | Cross-Model |
|----------|--------|-------|-------------|
| Greenfield (strict tsconfig) | Trivial — enable all as error | Define taste rules upfront | Add AGENTS.md |
| Migration (loose tsconfig) | Calibrate: warn → batch fix → error | Start with Stop hook only | Add AGENTS.md |
| Monorepo | tsconfig.eslint.json per package | Hooks apply repo-wide | One AGENTS.md at root |

Key anti-patterns summary table:

| Anti-Pattern | Why It Fails | Fix |
|-------------|-------------|-----|
| "We'll add lint later" | Tech debt compounds — 500 violations is unfixable | Start on day 1, even if all rules are "warn" |
| Using `type: "prompt"` hooks | Non-deterministic — can be steered | Always `type: "command"` |
| Stop hook diffs `HEAD` | Shows entire file, not changes | Diff `HEAD~1..HEAD` |
| Skipping calibration | All errors → all disabled | Run first, count violations, stratify |
| Only cross-model review | Misses everything lint and hooks catch | Layer 3 supplements, doesn't replace |
| grep to estimate violations | Off by 5x — rules interact | Run actual lint, count actual output |

**Footer (lines ~390-400):**
- Cross-link to back-pressure.md
- Cross-link to validation-gates.md
- Standard `*Next:*` footer

### Verification

- Read complete doc for coherence and accuracy against proven patterns
- Confirm line count < 400
- Confirm no project-specific references (no company names, no specific repos)
- Confirm matches existing methodology doc format: H1 → subtitle → intro → diagram → sections → tables → footer

### Files created

| File | Lines (est.) | Purpose |
|------|-------------|---------|
| `docs/methodology/evals-system.md` | ~390 | 3-layer evals system methodology |

---

## Phase 2: Diagrams

**Goal:** Create `docs/diagrams/` with 8 Mermaid diagram files + index README.

### What to create

This is the first Mermaid usage in the repo (existing diagrams are ASCII and SVG). Each file is self-contained: title, Mermaid code block, "When to use" note.

**Diagram specifications:**

#### 1. `fic-workflow.md` — FIC Workflow
```
flowchart LR
  Research → Learning Gate → Plan → Approach Gate → Implement → Implementation Gate → Compound
```
- Decision diamonds at each gate
- Shows the `/learn` branch for external integrations
- When to use: explaining the core FIC methodology to new users

#### 2. `evals-pyramid.md` — 3-Layer Evals Pyramid
```
graph TB (inverted pyramid or layered blocks)
  Layer 3: Cross-Model Review (top, narrowest)
  Layer 2: Claude Code Hooks (middle)
  Layer 1: ESLint Type-Checked Rules (base, widest)
```
- Labels showing determinism gradient (most → least)
- When to use: explaining the evals system overview

#### 3. `back-pressure-pyramid.md` — Back Pressure Pyramid
```
graph TB
  Type Check → Build → Tests → Integration → Learning Tests → Visual → LLM Review
```
- Color coding: green (deterministic) → yellow → red (probabilistic)
- When to use: explaining the verification hierarchy

#### 4. `context-engineering.md` — Context Engineering
```
graph LR or pie chart style
  40% budget visualization
  Shows: project context, conversation, tool results, reserved
```
- Fresh context triggers (60% threshold, 3 repeated errors)
- When to use: explaining context management to users hitting limits

#### 5. `agent-selection.md` — Agent Selection Flowchart
```
flowchart TD
  Start → Is it a quick task?
  Yes → Direct execution
  No → Does it need research?
  Yes → Explore agent
  No → Does it need a plan?
  Yes → Plan agent
  ...
```
- Decision tree format
- When to use: deciding which Claude Code agent pattern to use

#### 6. `session-lifecycle.md` — Session Lifecycle
```
flowchart LR
  Start (load PACK) → FIC Loop (Research → Implement → Compound) → End (Transfer Pack)
```
- Shows the recurring FIC loop within a session
- When to use: understanding the full session flow

#### 7. `hook-execution-flow.md` — Hook Execution Flow
```
flowchart LR
  PreToolUse gate → Tool executes → PostToolUse advisory → ... → Stop self-review
```
- Shows gate (blocking) vs advisory (non-blocking) distinction
- When to use: understanding how Claude Code hooks interact with tool execution

#### 8. `dual-verification.md` — Dual Verification Model
```
flowchart LR
  Claude Code implements → PR created → Codex auto-review → Human review → Codex manual review (optional)
```
- Shows where each verification happens in the PR lifecycle
- When to use: understanding the cross-model review workflow

#### `README.md` — Diagram Index

Table format:

| Diagram | File | When to Use |
|---------|------|-------------|
| FIC Workflow | [fic-workflow.md](fic-workflow.md) | Explaining the core development loop |
| ... | ... | ... |

Plus a note that these are Mermaid diagrams rendered natively by GitHub.

### Verification

- Each Mermaid diagram must use valid syntax (test by previewing on GitHub or a Mermaid renderer)
- Each file must be self-contained (title + diagram + "When to use" note)
- README.md links must resolve correctly
- No project-specific references in any diagram

### Files created

| File | Lines (est.) | Purpose |
|------|-------------|---------|
| `docs/diagrams/README.md` | ~35 | Index of all diagrams |
| `docs/diagrams/fic-workflow.md` | ~30 | FIC workflow with gates |
| `docs/diagrams/evals-pyramid.md` | ~30 | 3-layer evals pyramid |
| `docs/diagrams/back-pressure-pyramid.md` | ~30 | Verification hierarchy |
| `docs/diagrams/context-engineering.md` | ~30 | Context budget visualization |
| `docs/diagrams/agent-selection.md` | ~35 | Agent type decision tree |
| `docs/diagrams/session-lifecycle.md` | ~25 | Full session flow |
| `docs/diagrams/hook-execution-flow.md` | ~30 | Hook types and execution order |
| `docs/diagrams/dual-verification.md` | ~25 | Cross-model review workflow |

---

## Phase 3: Cross-Links and Release

**Goal:** Wire everything together — update TOC, changelog, and cross-references.

### Changes to existing files

#### `README.md`

Update Table of Contents (inside `<details>` block, lines 118-154):

Add under **Methodology** section:
```markdown
  - [Evals System](docs/methodology/evals-system.md)
```

Add new **Diagrams** section:
```markdown
- **Diagrams**
  - [All Diagrams](docs/diagrams/README.md) — Mermaid visual references
```

Update version badge from `2.2.0` to `2.3.0`.

#### `CHANGELOG.md`

Add new entry at line 9 (after `---`, before `## [2.2.0]`):

```markdown
## [2.3.0] - 2026-03-19

### Added
- `docs/methodology/evals-system.md` — 3-layer automated quality enforcement (ESLint type-checked rules → Claude Code hooks → cross-model review)
- `docs/diagrams/` — 8 Mermaid diagrams for FIC workflow, evals pyramid, back pressure, context engineering, agent selection, session lifecycle, hook execution, and dual verification
- Evals System and Diagrams added to README table of contents

### Attribution
- Proven across two production codebases (50+ Claude Code sessions)
- Claude Code hooks system (Anthropic Claude Code team)
- OpenAI Codex cross-model review patterns

---
```

#### `docs/methodology/back-pressure.md`

Add cross-link before the footer (before line 152 `---`):

```markdown
## Beyond Back Pressure: Automated Enforcement

Back pressure tells you *what* to verify. The [Evals System](evals-system.md) tells you *how* to enforce it automatically — ESLint for deterministic checks, Claude Code hooks for taste violations, and cross-model review for architectural blind spots.
```

#### `docs/methodology/validation-gates.md`

Add cross-link before the footer (before line 142 `---`). Insert after the Recovery Protocol section:

```markdown
## Automating Gate Enforcement

Gates are manual checkpoints. The [Evals System](evals-system.md) automates the mechanical parts — lint catches type errors before you review, hooks catch anti-patterns before you see the diff, and cross-model review catches design issues before you merge.
```

### Verification

- All internal links resolve (relative paths correct)
- README TOC renders correctly in `<details>` block
- CHANGELOG follows Keep a Changelog format
- Version badge URL updated
- Cross-links in back-pressure.md and validation-gates.md use correct relative paths
- No broken existing links (cross-reference check)

### Files modified

| File | Change | Lines affected |
|------|--------|---------------|
| `README.md` | Add TOC entries, update version badge | ~5 lines added |
| `CHANGELOG.md` | Add v2.3.0 entry | ~15 lines added |
| `docs/methodology/back-pressure.md` | Add evals cross-link section | ~5 lines added |
| `docs/methodology/validation-gates.md` | Add evals cross-link section | ~5 lines added |

---

## Execution Order

```
Phase 1 ──► Phase 2 ──► Phase 3
(evals doc)  (diagrams)  (cross-links)
```

Phases are sequential because:
- Phase 2 diagrams reference concepts defined in Phase 1
- Phase 3 cross-links reference files created in Phases 1-2

Each phase is independently shippable — Phase 1 alone adds value, Phase 2 adds visual references, Phase 3 ties everything together.

## Total Impact

| Metric | Value |
|--------|-------|
| New files | 11 |
| Modified files | 4 |
| Estimated new lines | ~660 |
| New methodology coverage | Automated quality enforcement (was zero) |
| New visual assets | 8 Mermaid diagrams (first in repo) |
