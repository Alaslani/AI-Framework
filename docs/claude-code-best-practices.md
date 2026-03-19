# Claude Code Best Practices Guide

> A general-purpose guide for setting up and running Claude Code on any project.
> Extracted from battle-tested patterns on a production TypeScript/Next.js platform.

---

## Table of Contents

1. [Project Structure](#1-project-structure)
2. [CLAUDE.md Design](#2-claudemd-design)
3. [ESLint & Type Safety](#3-eslint--type-safety)
4. [Claude Code Hooks](#4-claude-code-hooks)
5. [Agent Design](#5-agent-design)
6. [Slash Commands](#6-slash-commands)
7. [Build & Deploy Pipeline](#7-build--deploy-pipeline)
8. [Authentication Patterns](#8-authentication-patterns)
9. [UI Patterns](#9-ui-patterns)
10. [Testing Patterns](#10-testing-patterns)
11. [Key Gotchas](#11-key-gotchas)
12. [Appendix: Quick Setup Checklist](#appendix-quick-setup-checklist)

---

## 1. Project Structure

### Monorepo Layout

Use Turborepo with a clear app/package split. Each app is a deployable unit; each package is a shared library.

```
your-project/
├── apps/
│   ├── web/                  # Main customer-facing app
│   ├── admin/                # Internal admin portal
│   └── api/                  # API service (optional)
├── packages/
│   ├── ui/                   # Shared component library
│   ├── types/                # Shared TypeScript types
│   ├── shared/               # Shared utilities
│   ├── i18n/                 # Internationalization
│   ├── config-typescript/    # Shared tsconfig presets
│   └── config-eslint/        # Shared ESLint presets
├── scripts/                  # Operational scripts (seeding, verification, CI)
├── tests/
│   ├── e2e/                  # Playwright E2E tests
│   └── unit/                 # Vitest unit tests (or colocate)
├── .claude/
│   ├── settings.json         # Claude Code config + hooks
│   ├── hooks/                # Hook scripts (bash)
│   ├── agents/               # Subagent definitions
│   ├── commands/             # Slash commands
│   ├── skills/               # Skill files
│   ├── docs/                 # Internal reference docs
│   ├── plans/                # Implementation plans
│   ├── research/             # Research outputs
│   └── learnings/            # Gotchas and learnings
├── CLAUDE.md                 # Main project instructions
├── CLAUDE.local.md           # Local overrides (gitignored)
├── AGENTS.md                 # Top-level agent roster + review rules
├── turbo.json                # Turborepo pipeline config
├── eslint.config.mjs         # Flat ESLint config
└── tsconfig.json             # Root TypeScript config
```

**Why this layout:**

- `apps/` vs `packages/` makes deployment boundaries explicit
- `.claude/` centralizes all AI-assistant configuration in one place
- `scripts/` keeps operational code separate from application code
- Shared packages prevent code duplication across apps

### When to Split Into Separate Apps

Split when you have:

- **Different auth strategies** (e.g., OTP for customers, SSO for admins)
- **Different deployment targets** (different domains, different Vercel projects)
- **Different permission models** (public vs internal)
- **Different UI requirements** (customer-facing vs internal tooling)

Keep as one app when differences are just routing — use middleware-based routing instead.

### Package Boundaries

Each shared package should:

- Have its own `package.json` with explicit exports
- Extend a shared `tsconfig` preset
- Never import from `apps/` — dependency flows one way: `apps → packages`

```json
// packages/types/package.json
{
  "name": "@your-org/types",
  "private": true,
  "exports": { ".": "./src/index.ts" },
  "devDependencies": {
    "@your-org/config-typescript": "workspace:*",
    "typescript": "^5.x"
  }
}
```

```json
// packages/types/tsconfig.json
{
  "extends": "@your-org/config-typescript/library.json",
  "compilerOptions": { "outDir": "dist" },
  "include": ["src/**/*.ts"]
}
```

---

## 2. CLAUDE.md Design

### Principles

1. **Stability for prompt caching.** Claude Code caches CLAUDE.md across turns. Frequent edits bust the cache and waste tokens.
2. **Progressive disclosure.** Put essentials in CLAUDE.md, details in skills/docs.
3. **Actionable over descriptive.** Every line should change Claude's behavior. If it doesn't, move it to a doc.

### What Goes in CLAUDE.md

| Include                       | Exclude                                         |
| ----------------------------- | ----------------------------------------------- |
| Quick-reference command table | Detailed workflow docs (put in `.claude/docs/`) |
| Tech stack summary            | Full API reference (put in `.claude/docs/`)     |
| File structure overview       | Component catalog (put in skills)               |
| Database column gotchas       | Historical decisions (put in memory)            |
| Financial formulas            | Meeting notes                                   |
| Auth strategy per portal      | Tutorial-style guides                           |
| Verification commands         | Full testing docs (put in skills)               |
| Branch/commit conventions     |                                                 |

### Structure Template

```markdown
# Project Name — Claude Code Instructions

> One-line description
> **Production**: your-domain.com

---

## Quick Reference

| Action    | Command                          |
| --------- | -------------------------------- |
| Research  | `/research [feature]`            |
| Plan      | `/plan [feature]`                |
| Implement | `/implement [feature] --phase N` |
| ...       | ...                              |

## 1. Tech Stack

(Table: Layer -> Technology)

## 2. Development Workflow

(Research -> Plan -> Implement, verification commands)

## 3. Code Standards

(File structure, naming, key patterns)

## 4. Database Rules

(Connection info, key tables, column name gotchas, financial formulas)

## 5. Internationalization

(Language strategy, RTL rules if applicable)

## 6. Testing

(Commands, conventions, where tests live)

## 7. Git Standards

(Branch naming, commit message format)

## 8. Verification Protocol

(What to run after changes, production URLs)

## 9. File Locations

(Pointer table to docs, skills, configs)

## 10. Agent Roster

(Table of agents with roles — details in agent files)
```

### CLAUDE.local.md

Use for personal preferences and local-only settings. Always gitignore it.

```markdown
# Local Overrides

## Personal Preferences

- Prefer concise responses during coding
- Detailed explanations for architecture decisions

## Local Testing

- Skip external API calls (use mocks)
- Local ports: Next.js 3000, DB Studio 54323

## Thinking Level

- Use "think hard" for database/security changes
- Use "ultrathink" for auth/financial code
```

**Why separate files:** CLAUDE.md is shared (checked in). CLAUDE.local.md is personal (gitignored). This prevents developer preference conflicts.

---

## 3. ESLint & Type Safety

### Flat Config with Type-Checked Rules

Use ESLint Flat Config (`eslint.config.mjs`) with `typescript-eslint`'s type-checked preset.

```js
// eslint.config.mjs
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";
import nextPlugin from "@next/eslint-plugin-next";

export default tseslint.config(
  // Base
  eslint.configs.recommended,
  ...tseslint.configs.recommendedTypeChecked,

  // TypeScript language service
  {
    languageOptions: {
      parserOptions: {
        projectService: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },
  },

  // Global TypeScript rules
  {
    files: ["**/*.{ts,tsx}"],
    rules: {
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/no-floating-promises": "error",
      "@typescript-eslint/prefer-as-const": "error",
      "no-console": ["warn", { allow: ["warn", "error"] }],
    },
  },

  // Next.js rules
  {
    plugins: { "@next/next": nextPlugin },
    rules: { ...nextPlugin.configs.recommended.rules },
  },

  // Ignores
  { ignores: ["node_modules/", ".next/", "dist/", "*.config.*"] }
);
```

### tsconfig.eslint.json Pattern

Create a dedicated tsconfig for ESLint that includes all linted files:

```json
// tsconfig.eslint.json
{
  "extends": "./tsconfig.json",
  "include": ["src/**/*.ts", "src/**/*.tsx", "eslint.config.mjs", "tests/**/*.ts"],
  "exclude": ["node_modules", ".next", "dist"]
}
```

**Why:** The TypeScript language service in ESLint needs to "see" every file it lints. Without this, you get "file not included in any tsconfig" errors.

### Custom Guard Rules

Beyond standard rules, add project-specific guards as ESLint rules or hook scripts:

```js
// API route guard — ensure correct patterns
{
  files: ["**/app/api/**/*.ts"],
  rules: {
    "no-restricted-syntax": ["error", {
      selector: "ExportNamedDeclaration[declaration.type='FunctionDeclaration']",
      message: "API routes must use the route handler pattern with auth checks."
    }],
  },
},

// Prevent wrong client usage (example: block raw SDK, enforce wrapper)
{
  files: ["**/app/**/*.ts", "**/app/**/*.tsx"],
  rules: {
    "no-restricted-imports": ["error", {
      paths: [{
        name: "your-raw-sdk-package",
        message: "Use the project wrapper (createServerClient / createBrowserClient) instead."
      }],
    }],
  },
},
```

### Calibration Workflow

When adding type-checked ESLint rules to an existing project:

1. Start with `warn` level for all new rules
2. Run `npx eslint . --format json | jq '.[] | .messages | length'` to count violations
3. Fix violations in batches (by directory or rule)
4. Promote to `error` once a rule has zero violations
5. Add to CI pipeline only after promotion

**Why:** Jumping straight to `error` on a large codebase creates hundreds of failures and blocks all work.

---

## 4. Claude Code Hooks

Hooks are shell scripts that run at specific lifecycle points. They are the **enforcement layer** — catching mistakes before they enter the codebase.

### Hook Types

| Type           | When                                   | Purpose                              |
| -------------- | -------------------------------------- | ------------------------------------ |
| `PreToolUse`   | Before a tool executes                 | Block dangerous operations           |
| `PostToolUse`  | After a tool executes                  | Quality checks on output             |
| `SessionStart` | Conversation begins, compacts, resumes | Inject context                       |
| `Stop`         | Before Claude's final response         | Self-review and anti-rationalization |

### Settings Configuration

```json
// .claude/settings.json
{
  "permissions": {
    "allow": ["Bash(npm run *)", "Bash(npx vitest *)", "Bash(npx playwright *)", "Bash(git *)"],
    "deny": ["Bash(rm -rf /)", "Bash(git push --force *)"]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/check-dangerous-commands.sh" }
        ]
      },
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/branch-guard.sh" },
          { "type": "command", "command": "bash .claude/hooks/file-guard.sh" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/check-post-write.sh" },
          { "type": "command", "command": "bash .claude/hooks/typecheck-changed.sh" },
          { "type": "command", "command": "bash .claude/hooks/check-any-type.sh" }
        ]
      }
    ],
    "SessionStart": [
      {
        "hooks": [{ "type": "command", "command": "bash .claude/hooks/session-context.sh" }]
      }
    ],
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/anti-rationalization.sh" },
          { "type": "command", "command": "bash .claude/hooks/self-review.sh" }
        ]
      }
    ]
  }
}
```

### Hook Templates

#### 1. Dangerous Command Blocker (PreToolUse -> Bash)

```bash
#!/bin/bash
# .claude/hooks/check-dangerous-commands.sh
# Blocks destructive CLI commands before execution

INPUT=$(cat)
CMD=$(echo "$INPUT" | jq -r '.tool_input.command // empty')
[ -z "$CMD" ] && exit 0

BLOCKED_PATTERNS=(
  "git push.*--force"
  "git reset --hard"
  "rm -rf /"
  "DROP TABLE"
  "DROP DATABASE"
  "TRUNCATE"
  "npm publish"
  "npx wrangler publish"
)

for pattern in "${BLOCKED_PATTERNS[@]}"; do
  if echo "$CMD" | grep -qiE "$pattern"; then
    echo "BLOCKED: Command matches dangerous pattern: $pattern"
    echo "If this is intentional, run manually in your terminal."
    exit 2
  fi
done
exit 0
```

**Why:** Claude can generate destructive commands. This catches them before execution, not after.

#### 2. Branch Guard (PreToolUse -> Edit/Write)

```bash
#!/bin/bash
# .claude/hooks/branch-guard.sh
# Prevents edits on main/master — enforces feature branch workflow

BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)

if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
  echo "BLOCKED: Cannot edit files on '$BRANCH' branch."
  echo "Create a feature branch first: git checkout -b feat/your-feature"
  exit 2
fi
exit 0
```

**Why:** Direct commits to main bypass CI and code review. This makes the PR workflow impossible to skip.

#### 3. File Guard (PreToolUse -> Edit/Write)

```bash
#!/bin/bash
# .claude/hooks/file-guard.sh
# Blocks editing sensitive files
# Reads file_path from stdin JSON (same as all other hooks)
set -euo pipefail

if ! command -v jq &>/dev/null; then exit 0; fi

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
[ -z "$FILE_PATH" ] && exit 0

BASENAME=$(basename "$FILE_PATH")
LOWER=$(echo "$BASENAME" | tr '[:upper:]' '[:lower:]')

# Block .env files
case "$LOWER" in
  .env|.env.*|env.local|env.production|env.development)
    echo "BLOCKED: $BASENAME is a sensitive file. Never edit environment files directly."
    exit 2 ;;
esac

# Block secrets, keys, certificates
case "$LOWER" in
  *secret*|*key*.pem|*.pem|*.p12|*.pfx|*credentials*|*private*key*)
    echo "BLOCKED: $BASENAME matches a sensitive file pattern."
    exit 2 ;;
esac

# Block hook config tampering
case "$FILE_PATH" in
  */.claude/settings.json|*/.claude/settings.local.json)
    echo "BLOCKED: .claude/settings.json cannot be edited by Claude. Edit manually."
    exit 2 ;;
esac

exit 0
```

#### 4. Post-Write Quality Check (PostToolUse -> Write/Edit)

```bash
#!/bin/bash
# .claude/hooks/check-post-write.sh
# Checks written files for common anti-patterns
# Reads file_path and content from stdin JSON
set -euo pipefail

if ! command -v jq &>/dev/null; then exit 0; fi

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
[ -z "$FILE_PATH" ] && exit 0

# Only check source files
case "$FILE_PATH" in
  *.ts|*.tsx|*.jsx) ;; # continue
  *) exit 0 ;;
esac

# Extract content from tool input (Write uses "content", Edit uses "new_string")
CONTENT=$(echo "$INPUT" | jq -r '.tool_input.content // .tool_input.new_string // empty')
[ -z "$CONTENT" ] && exit 0

# Check for console.log (non-logger files only)
if ! echo "$FILE_PATH" | grep -qE "(logger|debug|test|spec)"; then
  if echo "$CONTENT" | grep -qE 'console\.(log|debug|info)\('; then
    echo "WARNING: console.log found — use a logger or remove before commit"
  fi
fi

# Check for hardcoded magic numbers (customize the regex per project)
# Example: if echo "$CONTENT" | grep -qE '\b0\.025\b|\b0\.15\b'; then
#   echo "WARNING: Hardcoded constant found — import from config instead"
# fi

# Always exit 0 — these are warnings, not blockers
exit 0
```

#### 5. Type-Check Changed File (PostToolUse -> Write/Edit)

```bash
#!/bin/bash
# .claude/hooks/typecheck-changed.sh
# Runs tsc scoped to the changed file's nearest tsconfig
# Reads file_path from stdin JSON
set -euo pipefail

if ! command -v jq &>/dev/null; then exit 0; fi

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
[ -z "$FILE_PATH" ] && exit 0

# Only check .ts and .tsx files
case "$FILE_PATH" in
  *.ts|*.tsx) ;;
  *) exit 0 ;;
esac

# Resolve to absolute path if relative
if [[ "$FILE_PATH" != /* ]]; then
  FILE_PATH="$(pwd)/$FILE_PATH"
fi
[ ! -f "$FILE_PATH" ] && exit 0

# Find nearest tsconfig by walking up
DIR=$(dirname "$FILE_PATH")
TSCONFIG=""
while [ "$DIR" != "/" ]; do
  if [ -f "$DIR/tsconfig.json" ]; then
    TSCONFIG="$DIR/tsconfig.json"
    break
  fi
  DIR=$(dirname "$DIR")
done
[ -z "$TSCONFIG" ] && exit 0

PROJECT_ROOT=$(dirname "$TSCONFIG")
REL_PATH="${FILE_PATH#"$PROJECT_ROOT"/}"

OUTPUT=$(cd "$PROJECT_ROOT" && npx tsc --noEmit --pretty false --skipLibCheck 2>&1) || true
ERRORS=$(echo "$OUTPUT" | grep -F "$REL_PATH" | grep "error TS" || true)

if [ -n "$ERRORS" ]; then
  ERROR_COUNT=$(echo "$ERRORS" | wc -l | tr -d ' ')
  echo "Type errors in $REL_PATH ($ERROR_COUNT errors):"
  echo "$ERRORS" | head -10
  exit 2  # Block — type errors in the changed file
fi
exit 0
```

#### 6. Session Context Injector (SessionStart)

```bash
#!/bin/bash
# .claude/hooks/session-context.sh
# Injects git state and project health at session start

echo "=== Session Context ==="
echo "Branch: $(git rev-parse --abbrev-ref HEAD 2>/dev/null || echo 'unknown')"
echo "Last commit: $(git log -1 --oneline 2>/dev/null || echo 'none')"
echo "Uncommitted files: $(git status --porcelain 2>/dev/null | wc -l | tr -d ' ')"

# Optional: type error count
if command -v npx &>/dev/null && [ -f "tsconfig.json" ]; then
  ERRORS=$(npx tsc --noEmit 2>&1 | grep -c "error TS" || echo "0")
  echo "Type errors: $ERRORS (run 'npm run type-check' for details)"
fi
echo "==="
```

**Why:** Without this, every session starts cold. This gives Claude immediate awareness of project state.

#### 7. Anti-Rationalization (Stop Hook)

```bash
#!/bin/bash
# .claude/hooks/anti-rationalization.sh
# Detects when Claude may be avoiding work or rationalizing skips

INPUT=$(cat)
RESPONSE=$(echo "$INPUT" | jq -r '.stop_response // empty')
[ -z "$RESPONSE" ] && exit 0

PATTERNS=(
  "pre-existing|already existed|was there before"
  "out of scope|beyond the scope|not part of"
  "we can skip|let's skip|skipping"
  "not necessary|unnecessary|overkill"
  "works as expected|looks correct|seems fine"
)

for pattern in "${PATTERNS[@]}"; do
  if echo "$RESPONSE" | grep -qiE "$pattern"; then
    echo "Possible rationalization detected: '$pattern'"
    echo "If claiming something is pre-existing, verify with git blame."
    echo "If deferring work, confirm the user agrees."
    # Exit 0 — this is a nudge, not a block
    exit 0
  fi
done
exit 0
```

**Why:** LLMs sometimes rationalize skipping work ("that was already broken", "out of scope"). This hook surfaces the pattern so the user can decide.

#### 8. Self-Review / Taste Rules (Stop Hook)

```bash
#!/bin/bash
# .claude/hooks/self-review.sh
# Checks recent changes against project taste rules before finalizing

CHANGED_FILES=$(git diff --name-only HEAD 2>/dev/null)
[ -z "$CHANGED_FILES" ] && exit 0

ISSUES=""

for file in $CHANGED_FILES; do
  [ ! -f "$file" ] && continue
  echo "$file" | grep -qE "\.(ts|tsx)$" || continue

  # Rule 1: No explicit 'any'
  if grep -nE ": any\b|as any\b" "$file" >/dev/null 2>&1; then
    ISSUES="${ISSUES}\n  - Explicit 'any' type in $file"
  fi

  # Rule 2: No hardcoded user-facing strings (i18n projects)
  # Customize this regex for your project's language patterns
  # if grep -nP '[\x{0600}-\x{06FF}]' "$file" | grep -vE "(i18n|locale|translation)" >/dev/null 2>&1; then
  #   ISSUES="${ISSUES}\n  - Hardcoded non-English string in $file — use i18n"
  # fi

  # Rule 3: No console.log in production code
  if echo "$file" | grep -vqE "(test|spec|logger|debug)"; then
    if grep -nE "console\.(log|debug|info)\(" "$file" >/dev/null 2>&1; then
      ISSUES="${ISSUES}\n  - console.log in $file — remove or use logger"
    fi
  fi
done

if [ -n "$ISSUES" ]; then
  echo -e "Self-review found issues:$ISSUES"
fi
exit 0
```

**Why:** "Taste rules" catch subjective quality issues that ESLint can't. They encode your team's standards as automated checks.

### Hook Design Principles

1. **PreToolUse hooks should be fast** (<100ms). They run synchronously before every tool call.
2. **PostToolUse hooks can be slower** — they check output quality.
3. **Exit code 0** = pass (warning OK). **Exit code 2** = block the action.
4. **Never block on warnings in Stop hooks** — they're nudges, not gates.
5. **All hooks receive context via stdin JSON** — use `INPUT=$(cat)` then `jq -r '.tool_input.file_path'` to extract fields. Never use positional args (`$1`) or env vars for file paths.
6. **Keep hooks idempotent** — they may run multiple times per session.

---

## 5. Agent Design

### When to Use Agents

Use agents when:

- A task is **parallelizable** (research + implementation at the same time)
- A domain requires **specialized knowledge** (security audit, blockchain, i18n)
- You need to **protect context** (large search results stay in the agent's context)

Don't use agents when:

- A simple grep/read answers the question
- The task is sequential and single-domain
- You'd spend more tokens coordinating than doing

### Naming Convention

Give each agent:

- A **human name** (makes routing intuitive: "@Dana, write tests")
- A **role subtitle** (e.g., "Frontend Developer", "Security Auditor")
- Optionally a **localized name** if your team is multilingual

### Role Scoping Matrix

Every agent needs clear boundaries on what it can and cannot do:

| Category                  | Examples                                                        | Tool Access                            |
| ------------------------- | --------------------------------------------------------------- | -------------------------------------- |
| **Read-only auditors**    | Code reviewer, Security auditor, RLS validator, Build validator | Read, Grep, Glob, Bash (read commands) |
| **Research agents**       | Codebase librarian, External researcher                         | Read, Grep, Glob, WebSearch, WebFetch  |
| **Implementation agents** | Frontend dev, Backend architect, Blockchain dev                 | Full access (Read, Write, Edit, Bash)  |
| **Specialized fixers**    | Type enforcer, Code simplifier                                  | Read, Edit, Grep, Glob                 |
| **Test agents**           | Test writer, Test runner                                        | Full access                            |
| **Ops agents**            | DevOps, Migration validator                                     | Full access or read-only               |

**Why restrict tools:** A code reviewer that can edit files might "fix" issues instead of reporting them. A security auditor that can write files might accidentally introduce vulnerabilities. Tool restrictions enforce role discipline.

### Agent File Template

```markdown
---
name: agent-name
model: sonnet
description: One-line description of what this agent does
tools: Read, Grep, Glob
---

# Agent Name — Role Title

## Masteries

- Domain expertise area 1
- Domain expertise area 2
- Domain expertise area 3

## Scope

**In scope:** [what this agent handles]
**Out of scope:** [what to delegate elsewhere]
**Tool constraints:** [Read-only / Full access / specific tools]

## Methodology

1. UNDERSTAND — Read relevant files, trace code paths
2. ANALYZE — Identify patterns, anti-patterns, issues
3. REPORT — Structured output with file:line references
4. DELEGATE — Hand off to appropriate agent if out of scope

## Domain Context

[Project-specific knowledge this agent needs]

## Output Format

[Strict template: summary, findings table, recommendations]
Token limit: [e.g., 800 tokens max]

## Anti-Patterns

- WRONG: [common mistake]
- CORRECT: [right approach]

## Safety Rules

- NEVER [critical constraint]
- ALWAYS [mandatory behavior]

## Quality Checklist

Before completing, verify:

- [ ] All findings include file:line references
- [ ] Recommendations are actionable
- [ ] Nothing out of scope was attempted

## Handoff Table

| Situation            | Delegate To                         |
| -------------------- | ----------------------------------- |
| Needs implementation | @frontend-dev or @backend-architect |
| Security concern     | @security-auditor                   |
| Needs tests          | @test-writer                        |
```

### Agent Count Guidelines

| Project Size   | Recommended Agents                                   |
| -------------- | ---------------------------------------------------- |
| Solo / small   | 3-5 (researcher, implementer, reviewer, test runner) |
| Medium team    | 8-12 (add domain specialists)                        |
| Large platform | 15-20 (full roster with auditors)                    |

**Warning:** More agents = more coordination overhead. Every agent you add should have a clear, non-overlapping role.

### Parallel Agent Safety

**Core rule: One file = one owner.**

Before spawning 2+ agents simultaneously:

1. Map which files each agent will WRITE to
2. Check for overlaps (WRITE-WRITE = critical conflict)
3. If overlap: run sequentially instead
4. Always `git commit` before dispatching parallel agents (checkpoint)
5. After agents complete: verify build, accept or revert

Safe to parallelize:

- Frontend components + backend API routes (different directories)
- Tests + documentation (different file types)
- i18n files + implementation (different concerns)

Always sequential:

- Security audits (needs to see final state)
- Code simplification / refactoring (touches many files)
- Database migrations (order matters)
- Config/infrastructure changes

---

## 6. Slash Commands

### What Deserves a Command

Create a slash command when:

- You repeat the same multi-step workflow 3+ times
- The workflow has specific safety constraints
- You need to enforce a particular output format
- The task involves delegation to multiple agents

Don't create a command for:

- One-off tasks
- Simple tool calls (just ask Claude directly)
- Things that change frequently (put those in docs/skills instead)

### Command Categories

| Category        | Commands                                                | Pattern                                |
| --------------- | ------------------------------------------------------- | -------------------------------------- |
| **Workflow**    | `/research`, `/plan`, `/implement`                      | Research -> Plan -> Implement pipeline |
| **Quality**     | `/test`, `/security-scan`, `/perf-audit`, `/a11y-audit` | Audit + report                         |
| **Operations**  | `/deploy`, `/db-migrate`, `/db-check`                   | Execute + verify                       |
| **Maintenance** | `/commit`, `/session-end`, `/polish`                     | Cleanup + documentation                |
| **Quick**       | `/quick-task`, `/auto`                                  | Fast-track for small changes           |
| **Learning**    | `/learn`, `/tdd`                                        | Test-first workflows                   |

### Command Design Pattern

Every command should define:

1. **Role** — What persona Claude assumes
2. **Input** — What arguments/flags it accepts
3. **Steps** — Numbered workflow steps
4. **Guardrails** — What to NEVER do
5. **Output** — What the result looks like
6. **Verification** — How to confirm success

### Command Template

```markdown
---
description: "One-line description of what this command does"
---

# Command Name: $ARGUMENTS

## Role

You are a [role description].

## Input Parsing

- `$ARGUMENTS` = [what the user passes in]
- Flags: `--dry-run` (simulate only), `--verbose` (detailed output)

## Steps

### Step 1: [Name]

[What to do, which tools to use]

### Step 2: [Name]

[What to do next]

### Step 3: Verification

Run: `npm run type-check && npm run build`
If fails -> STOP and report errors. Do NOT continue.

## Guardrails

- NEVER [dangerous action]
- NEVER [another dangerous action]
- ALWAYS [required behavior]

## Output Format

[Template for what the user sees]

## Failure Modes

- If [condition] -> [recovery action]
- If [condition] -> STOP and ask user
```

### The FIC (Focused, Independent, Composable) Pattern

Commands should be:

- **Focused** — one clear purpose per command
- **Independent** — can run alone without prerequisites
- **Composable** — can be chained (`/research -> /plan -> /implement`)

The Research -> Plan -> Implement pipeline is the canonical example:

```
/research [feature]    -> Outputs: .claude/research/[feature].md (no code)
/plan [feature]        -> Reads research, outputs: .claude/plans/[feature].md (no code)
/implement [feature]   -> Reads plan, executes phase by phase (code)
```

**Why separate:** Research that writes code skips the design phase. Plans that implement skip review. Separation forces quality at each stage.

### Context Management in Commands

For long-running commands:

```markdown
## Context Management

- If context usage exceeds 40%: checkpoint progress, suggest new conversation
- Commit work every 5-10 files
- Phase-based execution: one context window per phase
- When resuming: read plan file to restore state
```

---

## 7. Build & Deploy Pipeline

### Turborepo Pipeline

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "dist/**"],
      "env": ["DATABASE_URL", "NEXT_PUBLIC_*"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "type-check": {
      "dependsOn": ["^build"]
    },
    "lint": {
      "dependsOn": ["^build"]
    },
    "test:unit": {
      "dependsOn": ["^build"]
    },
    "test:e2e": {
      "dependsOn": ["build"],
      "cache": false
    },
    "clean": {
      "cache": false
    }
  }
}
```

**Why `dependsOn: ["^build"]`:** Type-checking and linting need built packages (for cross-package type resolution). Without this, you get false errors from unresolved cross-package imports.

### Verification Stack (Pre-Deploy)

Run these checks in order — fail fast:

```bash
#!/bin/bash
# scripts/verify.sh — Run before every deploy

set -e  # Exit on first failure

echo "Step 1/7: Type check..."
npm run type-check

echo "Step 2/7: Build..."
npm run build

echo "Step 3/7: Lint..."
npm run lint

echo "Step 4/7: Unit tests..."
npm run test:unit

echo "Step 5/7: Code simplification..."
# Run /simplify in Claude Code before committing
# Catches: dead code, duplicate logic, guard divergence, unnecessary complexity

echo "Step 6/7: Check for stale references..."
# Grep for imports of deleted files
git diff --name-only --diff-filter=D HEAD~1 | while read deleted; do
  if grep -r "$(basename "$deleted" .ts)" src/ --include="*.ts" --include="*.tsx" -l 2>/dev/null; then
    echo "ERROR: Stale import of deleted file: $deleted"
    exit 1
  fi
done

echo "Step 7/7: E2E smoke tests..."
npm run test:e2e -- --grep @smoke

echo "All checks passed — safe to deploy"
```

### PR-Only Deployment (Never Push to Main)

```
Developer -> Feature Branch -> /simplify -> PR -> CI (auto) -> Code Review -> Merge -> Deploy
```

Enforce with:

1. **Branch protection rules** on GitHub (require PR, require CI pass)
2. **Branch guard hook** in Claude Code (prevents edits on main)
3. **Deploy command** that creates PRs, never pushes directly

### Build Skip Script (Monorepo)

For Vercel monorepo deployments, skip builds when only unrelated packages changed:

```bash
#!/bin/bash
# scripts/vercel-ignore.sh
# Returns exit 0 (build) or exit 1 (skip)

APP_NAME="${VERCEL_PROJECT_NAME:-web}"
DEPLOY_BRANCH="${VERCEL_GIT_COMMIT_REF}"

# Always build main
if [ "$DEPLOY_BRANCH" = "main" ]; then
  echo "Building: main branch"
  exit 0
fi

# Check if this app's files changed
CHANGED=$(git diff --name-only HEAD~1 HEAD 2>/dev/null || echo "FORCE_BUILD")

if echo "$CHANGED" | grep -qE "^apps/$APP_NAME/|^packages/"; then
  echo "Building: changes detected in $APP_NAME or shared packages"
  exit 0
fi

echo "Skipping: no relevant changes for $APP_NAME"
exit 1
```

### Post-Deploy Verification

After every deploy, verify each portal/app is working:

```markdown
## Post-Deploy Checklist

- [ ] App loads without errors (check browser console)
- [ ] Auth flow works (login/logout)
- [ ] Critical user journey works (e.g., create/read/update)
- [ ] API health endpoint returns 200
- [ ] No new errors in monitoring (Sentry, Vercel logs)
```

---

## 8. Authentication Patterns

### Multi-Portal Auth Strategy

When different user types need different auth flows, use separate apps with shared auth infrastructure:

| Portal     | Auth Method          | Why                                    |
| ---------- | -------------------- | -------------------------------------- |
| Customer   | SMS OTP / Magic Link | Low friction, mobile-first             |
| Internal   | SSO / Email+Password | Behind VPN or IP allowlist             |
| Admin      | MFA-enforced         | Highest privilege, strictest controls  |

### Implementation Pattern

```
                    +------------------+
                    |  Auth Provider   |  Your auth provider (Auth0, Clerk, Supabase Auth, etc.)
                    |  (shared)        |
                    +--------+---------+
                             |
              +--------------+--------------+
              |              |              |
        +-----+------+ +----+-----+ +-----+-----+
        | Customer   | | Internal | | Admin     |
        | App        | | App      | | App       |
        | OTP        | | SSO      | | MFA       |
        +------------+ +----------+ +-----------+
```

### Generalized Rules

1. **Auth check in every API route** — never assume the request is authenticated
2. **Use server-side auth verification** — never trust client-side tokens alone
3. **Role-based access at the database level** (RLS) — not just application level
4. **Separate auth flows = separate apps** — middleware routing for simple cases, separate deployments for complex ones
5. **Admin operations use elevated client** — with explicit privilege escalation
6. **Never log auth tokens or secrets** — even in development

```typescript
// Pattern: Auth check in every API route
export async function GET(request: Request) {
  const user = await getAuthenticatedUser();
  if (!user) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  // Check role
  if (!hasRole(user, "admin")) {
    return NextResponse.json({ error: "Forbidden" }, { status: 403 });
  }

  // Proceed with authorized logic...
}
```

---

## 9. UI Patterns

### Shared Component Library

Structure your shared UI package with clear categories:

```
packages/ui/src/
├── primitives/           # Base components (Button, Input, Card, Dialog)
│   └── index.ts          # Barrel export
├── domain/               # Business-specific components
│   └── index.ts
├── charts/               # Data visualization
│   └── index.ts
├── auth/                 # Auth UI (LoginForm, OTPInput)
│   └── index.ts
└── index.ts              # Main barrel export
```

**Component export rules:**

- Export everything from `primitives/` (always needed)
- Selective exports from `domain/` and `charts/` (to enable tree-shaking)
- Add performance notes for heavy components: `// Import directly for tree-shaking`

### RTL / Bilingual Support

If your app supports RTL languages (Arabic, Hebrew, Farsi):

```tsx
// Pattern: RTL container
<div dir={isRTL(locale) ? "rtl" : "ltr"}>{children}</div>
```

**Rules:**

1. **Use logical CSS properties** — `ps-4` (padding-start) not `pl-4` (padding-left)
2. **Keep numbers LTR** — even in RTL contexts, numbers read left-to-right
3. **Mirror icons** that indicate direction — arrows, chevrons
4. **Don't mirror** — logos, checkmarks, universal icons
5. **Test both directions** — RTL layout breaks are invisible if you only test LTR

| Physical (avoid)          | Logical (use)            |
| ------------------------- | ------------------------ |
| `pl-4`, `pr-4`            | `ps-4`, `pe-4`           |
| `ml-4`, `mr-4`            | `ms-4`, `me-4`           |
| `left-0`, `right-0`       | `start-0`, `end-0`       |
| `text-left`, `text-right` | `text-start`, `text-end` |
| `border-l`, `border-r`    | `border-s`, `border-e`   |

### Translation Pattern

```typescript
// Localized text object pattern
interface LocalizedText {
  [locale: string]: string; // e.g., { en: "Hello", es: "Hola" }
}

// Usage in components
function PageTitle({ title }: { title: LocalizedText }) {
  const { locale } = useLocale();
  return <h1>{title[locale]}</h1>;
}
```

### Tailwind v4 Monorepo Gotchas

When using Tailwind v4 in a monorepo:

- Each app needs its own Tailwind config that scans `packages/ui/src/**/*.tsx`
- Shared packages shouldn't ship compiled CSS — let apps handle compilation
- Use `@source` directive in app configs to include package paths

### Design System Enforcement

Encode your design system as machine-checkable rules:

```js
// ESLint rule: enforce design system colors
{
  files: ["**/components/**/*.tsx"],
  rules: {
    "no-restricted-syntax": ["warn", {
      selector: "JSXAttribute[name.name='className'][value.value=/(?:bg|text|border)-(?:red|blue|green)-/]",
      message: "Use design system color tokens (e.g., bg-primary, text-muted) instead of raw colors."
    }]
  }
}
```

---

## 10. Testing Patterns

### Test Structure

```
tests/
├── e2e/
│   ├── smoke/             # Quick verification (run every deploy)
│   │   └── health.spec.ts
│   ├── auth.spec.ts       # Auth flow tests
│   ├── critical-path.spec.ts  # Critical user journeys
│   └── ...
├── unit/
│   └── (colocated or centralized — pick one)
└── integration/
    └── api/               # API route tests
```

### data-testid Convention

Add `data-testid` to all interactive elements:

```tsx
<button data-testid="submit-form">Submit</button>
<input data-testid="email-input" />
<div data-testid="total-count">{count}</div>
```

**Naming pattern:** `[action]-[noun]` or `[noun]-[variant]`

- Buttons: `submit-form`, `cancel-order`, `open-modal`
- Inputs: `email-input`, `amount-input`, `search-input`
- Displays: `total-count`, `status-badge`, `error-message`
- Containers: `user-card`, `item-list`, `empty-state`

### Test Naming

```typescript
describe("OrderService", () => {
  it("calculates total with fee and tax", () => { ... });
  it("rejects order below minimum amount", () => { ... });
  it("rounds to 2 decimal places", () => { ... });
});
```

**Pattern:** `it("[verb]s [what] [condition]")`

- Starts with a verb (calculates, rejects, returns, renders, displays)
- Describes the behavior, not the implementation
- Includes edge case conditions when relevant

### Test Factories

Create reusable test data factories:

```typescript
// tests/factories.ts
export function createMockUser(overrides?: Partial<User>): User {
  return {
    id: crypto.randomUUID(),
    email: "test@example.com",
    role: "member",
    created_at: new Date().toISOString(),
    ...overrides,
  };
}

export function createMockOrder(overrides?: Partial<Order>): Order {
  return {
    id: crypto.randomUUID(),
    user_id: crypto.randomUUID(),
    amount: 10000,
    status: "confirmed",
    ...overrides,
  };
}
```

### Financial Test Patterns

For applications with money:

```typescript
it("rounds total to 2 decimal places", () => {
  // Use Math.round(x * 100) / 100 — NOT toFixed()
  expect(calculateTotal(100)).toBe(110.00); // Normal case (e.g., 10% tax)
  expect(calculateTotal(0.01)).toBe(0.01);  // Edge: tiny amount
  expect(calculateTotal(99999)).toBe(109998.9); // Edge: large amount
});
```

### E2E Test Commands

```json
// package.json scripts
{
  "test:unit": "vitest run",
  "test:unit:watch": "vitest --watch",
  "test:unit:coverage": "vitest run --coverage",
  "test:e2e": "playwright test",
  "test:e2e:headed": "playwright test --headed",
  "test:e2e:ui": "playwright test --ui",
  "test:smoke": "playwright test --grep @smoke"
}
```

### TDD Workflow

```
1. Write failing test (RED)     -> commit test
2. Implement minimal code (GREEN) -> commit implementation
3. Refactor (optional)           -> commit refactor
4. NEVER modify the test to make it pass
```

---

## 11. Key Gotchas

### Universal Rules (Every Project)

| #   | Rule                                                           | Why                                                                        |
| --- | -------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1   | **Never push directly to main**                                | Bypasses CI, review, and deployment gates                                  |
| 2   | **Run type-check before build**                                | Build may succeed with type errors (especially with `skipLibCheck`)        |
| 3   | **Never commit `.env` files**                                  | Secrets leak into git history permanently                                  |
| 4   | **Delete `.next`/`dist` cache when debugging phantom errors**  | Stale build artifacts cause false positives/negatives                      |
| 5   | **Import from canonical source files, never inline constants** | Magic numbers drift between files over time                                |
| 6   | **Use `Math.round(x * 100) / 100` for money, not `toFixed()`** | `toFixed()` returns a string and has rounding inconsistencies              |
| 7   | **Add RLS policies on every new database table**               | A table without RLS is publicly readable by any authenticated user         |
| 8   | **Always verify column names against the actual schema**       | Column name assumptions (e.g., `amount` vs `total_paid`) cause silent bugs |
| 9   | **Test both auth'd and unauth'd paths in API routes**          | Missing auth checks are invisible until exploited                          |
| 10  | **Never trust client-sent IDs for authorization**              | Always derive user identity from the server-side session                   |

### Claude Code Specific Rules

| #   | Rule                                                        | Why                                                                   |
| --- | ----------------------------------------------------------- | --------------------------------------------------------------------- |
| 11  | **Keep CLAUDE.md stable**                                   | Every edit busts the prompt cache, wasting tokens                     |
| 12  | **Use skills for detailed docs, not CLAUDE.md**             | Skills load on-demand; CLAUDE.md loads every turn                     |
| 13  | **Restrict agent tools to their role**                      | A reviewer that can edit will "fix" instead of report                 |
| 14  | **Use Stop hooks for taste enforcement**                    | ESLint catches syntax; hooks catch judgment calls                     |
| 15  | **Checkpoint (git commit) before parallel agents**          | If agents conflict, you need a clean rollback point                   |
| 16  | **Anti-rationalization hooks prevent work avoidance**       | LLMs naturally minimize effort; hooks counteract this                 |
| 17  | **Session context hooks prevent cold starts**               | Without state injection, Claude re-discovers project state every time |
| 18  | **Never modify tests to make them pass** (TDD)              | The test defines the contract; the implementation must meet it        |
| 19  | **Separate Research/Plan/Implement into distinct commands** | Mixing phases leads to premature implementation or skipped design     |
| 20  | **Use `exit 2` (not `exit 1`) to block in hooks**           | Claude Code uses exit code 2 specifically for "block this action"    |

### Monorepo Specific Rules

| #   | Rule                                                         | Why                                                       |
| --- | ------------------------------------------------------------ | --------------------------------------------------------- |
| 21  | **`dependsOn: ["^build"]` for type-check and lint**          | Cross-package types aren't available until packages build |
| 22  | **Never import from `apps/` in `packages/`**                 | Dependency must flow one way: apps -> packages            |
| 23  | **Each app needs its own Tailwind config scanning packages** | Shared packages don't compile their own CSS               |
| 24  | **Use workspace protocol for internal deps** (`workspace:*`) | Ensures local resolution, not npm registry                |
| 25  | **Extend shared tsconfig presets, don't copy them**          | Divergent configs cause inconsistent type behavior        |

### Build & Deploy Rules

| #   | Rule                                                              | Why                                                                            |
| --- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| 26  | **Deleting API routes leaves stale type references in `.next`**   | `tsc` reports false errors until cache is cleaned                              |
| 27  | **Run full verification (`--dry-run`) before production deploys** | If you have staging, use it. If not, the verification stack is your safety net |
| 28  | **Verify all portals after deploy, not just the one you changed** | Shared package changes can break other apps                                    |
| 29  | **Use `--dry-run` before real deploys**                           | Catches issues before they're live                                             |
| 30  | **Build skip scripts save CI minutes in monorepos**               | Without them, every PR builds every app                                        |

---

## Appendix: Quick Setup Checklist

Starting a new Claude Code project? Do these in order:

- [ ] Create `CLAUDE.md` with tech stack, file structure, and key rules
- [ ] Create `.claude/settings.json` with permissions and hooks
- [ ] Add branch guard hook (prevent edits on main)
- [ ] Add file guard hook (protect `.env` and secrets)
- [ ] Add dangerous command blocker hook
- [ ] Add session context hook (git state injection)
- [ ] Add post-write quality check hook
- [ ] Add anti-rationalization Stop hook
- [ ] Create 3-5 core agents (researcher, implementer, reviewer, test runner)
- [ ] Create core commands (`/research`, `/plan`, `/implement`, `/commit`, `/deploy`)
- [ ] Set up ESLint with type-checked rules
- [ ] Set up Turborepo pipeline (if monorepo)
- [ ] Add `CLAUDE.local.md` to `.gitignore`
- [ ] Create `.claude/learnings/gotchas.md` for discovered issues
- [ ] Create `AGENTS.md` with review rules and blocking criteria

---

_Generated from production patterns. Adapt to your stack, team size, and regulatory requirements._
