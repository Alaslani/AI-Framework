# Evals System

Automated quality enforcement for AI-assisted development.

## The Problem

[Back Pressure](back-pressure.md) tells you *what* to verify — type checkers,
builds, tests. [Validation Gates](validation-gates.md) tell you *when* to verify —
between phases, before deployment. But neither answers: **who enforces this
when the human isn't watching?**

Evals are the enforcement layer. They catch errors automatically — before
Claude finishes, before the PR merges, before a human reviews. Three layers,
each catching what the one below can't.

> The goal is not to replace human review. It's to ensure that by the time
> a human looks at the code, the mechanical problems are already gone.

## The Three Layers

```
Least Deterministic
┌───────────────────────────────────────┐
│  Layer 3: Cross-Model Review          │  ← Design, architecture, blind spots
├───────────────────────────────────────┤
│  Layer 2: Claude Code Hooks           │  ← Taste, rationalization, domain rules
├───────────────────────────────────────┤
│  Layer 1: ESLint Type-Checked Rules   │  ← Types, async, promises, any-leakage
└───────────────────────────────────────┘
Most Deterministic
```

Each layer is more deterministic than the one above it. Start from the bottom.
The lower layers are cheaper, faster, and more reliable — they should catch
the most issues.

---

## Layer 1: ESLint Type-Checked Rules

The foundation. Deterministic, millisecond-fast, catches entire categories
of bugs that even careful human review misses.

### Why Type-Checked Rules

Standard ESLint rules check syntax. Type-checked rules use TypeScript's
compiler to catch deeper problems — unhandled promises, `any` leaking through
generics, awaiting non-thenables. These are bugs that compile fine but fail
at runtime.

> Same code, same rule, same result. Every time. That's what makes this
> the foundation — it can't be steered.

### Setup

ESLint 9+ flat config with `typescript-eslint` type-checked preset:

```js
// eslint.config.mjs
import tseslint from "typescript-eslint";

export default tseslint.config(
  ...tseslint.configs.recommendedTypeChecked,
  {
    languageOptions: {
      parserOptions: {
        projectService: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },
  }
);
```

For monorepos, create a `tsconfig.eslint.json` per package that extends
the base config. This gives each package its own type-checking scope without
duplicating compiler settings.

### Key Rules

| Rule | What It Catches | Why It Matters |
|------|----------------|----------------|
| `no-floating-promises` | Unhandled promise rejections | Silent failures in production |
| `no-misused-promises` | Promises in boolean context | Conditionals that always pass |
| `await-thenable` | Awaiting non-promises | Misleading code, hides bugs |
| `no-explicit-any` | Type safety escape hatches | Undermines entire type system |
| `no-unsafe-assignment` | `any` spreading through assignment | One `any` infects everything it touches |
| `no-unsafe-return` | `any` leaking from function returns | Callers lose type safety silently |

### Calibration Workflow

This is the non-obvious part. You cannot guess violation counts.

**Lesson: grep estimates are 5x off.** Rules interact — `no-unsafe-assignment`
triggers on patterns that look nothing like `any` in a grep. Always run
actual lint and count real output.

The calibration process:

1. **Run lint** on the full codebase — get actual violation counts per rule
2. **Zero-violation rules** → set to `"error"` immediately (free wins)
3. **High-violation rules** → set to `"warn"`, fix in batches
4. **Track counts** — when a rule hits zero violations, promote to `"error"`
5. **Repeat** until all rules are `"error"`

```bash
# Count violations per rule (actual numbers, not grep estimates)
npx eslint . --format json 2>/dev/null \
  | jq '[.[] | .messages[] | .ruleId] | group_by(.) | map({rule: .[0], count: length}) | sort_by(-.count)'
```

### Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Skip type-checked rules → miss the highest-value catches | Use `recommendedTypeChecked` from day one |
| Leave `"warn"` rules without a promotion deadline | Promote to `"error"` when count hits zero, or remove |

---

## Layer 2: Claude Code Hooks

Lint catches what's syntactically wrong. Hooks catch what's *technically valid
but wrong for your project* — rationalization, taste violations, domain-specific
anti-patterns. Things a type checker will never flag.

### Why Hooks

Claude Code hooks are shell scripts that run at specific points during a
session. They're deterministic (same input → same output), auditable (it's
just bash), and versionable (commit them to the repo).

> All hooks must be `type: "command"`. Never `"prompt"` or `"agent"` —
> those are probabilistic, defeating the entire purpose of automated enforcement.

### Hook Configuration

Hooks live in `.claude/settings.json` (shared with team) or
`.claude/settings.local.json` (personal). Shell scripts go in a `.claude/hooks/`
directory, committed to the repo.

```jsonc
// .claude/settings.json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "type": "command",
        "command": ".claude/hooks/guard-protected-paths.sh"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "type": "command",
        "command": ".claude/hooks/check-file-length.sh"
      }
    ],
    "Stop": [
      {
        "type": "command",
        "command": ".claude/hooks/self-review.sh"
      }
    ]
  }
}
```

### PreToolUse — Gates

Runs before a tool executes. Exit 0 to allow, non-zero to block. The hook
receives the tool name and parameters on stdin as JSON.

```bash
#!/bin/bash
# .claude/hooks/guard-protected-paths.sh
# Block writes to migration files, lockfiles, and CI config
# without explicit acknowledgment.

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

if [ -z "$FILE_PATH" ]; then
  exit 0
fi

PROTECTED_PATTERNS=(
  "migrations/"
  "db/migrate/"
  ".github/workflows/"
  "package-lock.json"
  "yarn.lock"
  "pnpm-lock.yaml"
)

for pattern in "${PROTECTED_PATTERNS[@]}"; do
  if [[ "$FILE_PATH" == *"$pattern"* ]]; then
    echo "BLOCKED: $FILE_PATH matches protected pattern '$pattern'"
    echo "If this change is intentional, ask the user to confirm."
    exit 1
  fi
done

exit 0
```

Keep PreToolUse hooks fast (<2s). They gate every matching tool call.

### PostToolUse — Advisories

Runs after a tool executes. Output is shown to Claude as advice but doesn't
block. Good for soft rules that need judgment.

```bash
#!/bin/bash
# .claude/hooks/check-file-length.sh
# Warn when a file grows beyond a threshold.
# Advisory only — Claude decides whether to refactor.

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  exit 0
fi

LINE_COUNT=$(wc -l < "$FILE_PATH" | tr -d ' ')
THRESHOLD=400

if [ "$LINE_COUNT" -gt "$THRESHOLD" ]; then
  echo "WARNING: $FILE_PATH is $LINE_COUNT lines (threshold: $THRESHOLD)"
  echo "Consider splitting this file. Large files reduce readability"
  echo "and make future changes harder to review."
fi

exit 0
```

### Stop — Self-Review

Runs when Claude signals it's done. This is where you catch rationalization
and taste violations in the final diff.

> **Critical:** use `git diff HEAD` (uncommitted changes vs last commit),
> not `git show HEAD` or a bare `git diff` (which omits staged changes).

```bash
#!/bin/bash
# .claude/hooks/self-review.sh
# Check uncommitted changes for anti-patterns before Claude finishes.

DIFF=$(git diff HEAD 2>/dev/null)

if [ -z "$DIFF" ]; then
  exit 0
fi

ISSUES=""

# Anti-rationalization: catch Claude making excuses instead of fixing
for pattern in "for now" "we can fix later" "TODO.*workaround" \
               "temporary solution" "good enough for" "skip.*for now"; do
  MATCHES=$(echo "$DIFF" | grep -in "$pattern" | head -3)
  if [ -n "$MATCHES" ]; then
    ISSUES+="RATIONALIZATION ('$pattern'): $MATCHES"$'\n'
  fi
done

# Taste: catch common code smells in the diff
for pattern in "eslint-disable" "@ts-ignore" "@ts-expect-error" \
               "as any" "console\.log"; do
  COUNT=$(echo "$DIFF" | grep -c "$pattern" 2>/dev/null)
  if [ "$COUNT" -gt 0 ]; then
    ISSUES+="TASTE: $COUNT occurrence(s) of '$pattern' in diff"$'\n'
  fi
done

if [ -n "$ISSUES" ]; then
  echo "=== Self-Review Findings ==="
  echo "$ISSUES"
  echo "Fix what's fixable, justify what's intentional."
  exit 1
fi

exit 0
```

### Domain-Specific Taste Rules

The hooks above are generic. Each project should add its own. Examples:

| Project Type | Taste Rule | Hook Check |
|-------------|-----------|------------|
| Web app | No inline styles | grep for `style=` in JSX |
| API service | No raw SQL outside query layer | grep for `SELECT\|INSERT\|UPDATE` outside `queries/` |
| Blockchain | No floating-point for amounts | grep for `parseFloat\|toFixed` near currency vars |
| Monorepo | No cross-package direct imports | grep for imports violating package boundaries |

The key: these are rules that a linter can't express because they require
project context. Hooks fill that gap.

### Hook Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Use `type: "prompt"` → probabilistic | Always `type: "command"` |
| Stop hook diffs wrong target → misses uncommitted changes | Diff `HEAD` (uncommitted vs last commit) |
| Hooks that take >5s → developers disable them | Keep under 2s |
| Too many PreToolUse gates → blocks workflow | Start with 1-2, add as needed |
| Hooks with no output on failure → silent blocking | Always print why you blocked |

---

## Layer 3: Cross-Model Review

The model that wrote the code has blind spots about its own output. A second
model — different architecture, different training — catches what the first
missed. This is the least deterministic layer but catches the highest-level
issues: design flaws, missed requirements, architectural drift.

### Setup

Create an `AGENTS.md` at the repo root. This gives the reviewing model
project context — without it, reviews are generic and low-value.

Structure `AGENTS.md` with two rule categories:

```markdown
# AGENTS.md

## Blocking Rules (must fix before merge)
- All type errors must be resolved
- No new `eslint-disable` without justification comment
- Tests must cover the primary path of new functions
- No secrets, API keys, or credentials in code

## Non-Blocking Rules (suggestions)
- Prefer named exports over default exports
- Keep functions under 40 lines
- Use descriptive variable names (no single letters outside loops)
```

### Auto vs Manual Review

Configure auto-review to trigger on PR open. Use manual deep review for
significant changes.

| Change Type | Auto Review | Manual Review |
|-------------|------------|---------------|
| Bug fix (< 50 lines) | ✅ Sufficient | Skip |
| Feature (50-200 lines) | ✅ First pass | ✅ If touching core paths |
| Architecture change | ✅ First pass | ✅ Required |
| Dependency update | ✅ Sufficient | Skip |

**Key finding:** auto and manual reviews stack. Auto catches N issues (style,
obvious bugs). Manual catches M different issues (design, missing edge cases).
They don't overlap — they complement.

### Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Rely on cross-model review alone (skip layers 1-2) | Layer 3 supplements, never replaces |
| Treat all findings as blocking → review fatigue | Separate blocking from non-blocking rules |
| No `AGENTS.md` → reviewer has no project context | Write project-specific rules |
| Review every PR with manual deep review → slow | Reserve manual for architecture changes |

---

## Implementation Playbook

Each layer builds on the previous. Don't skip ahead.

1. **ESLint first** — deterministic baseline. Trivial for greenfield with
   strict `tsconfig.json`; calibration workflow for migrations.
2. **Claude Code hooks** — taste enforcement after lint is clean. Start with
   the Stop self-review hook. Add PreToolUse guards as problems emerge.
3. **Cross-model review** — after hooks catch the obvious stuff. Write
   `AGENTS.md`, enable auto-review on PRs.

### Greenfield vs Migration

| Scenario | ESLint | Hooks | Cross-Model |
|----------|--------|-------|-------------|
| Greenfield (strict tsconfig) | Enable all rules as `"error"` | Define taste rules upfront | Add `AGENTS.md` |
| Migration (loose tsconfig) | Calibrate: `"warn"` → batch fix → `"error"` | Start with Stop hook only | Add `AGENTS.md` |
| Monorepo | `tsconfig.eslint.json` per package | Hooks apply repo-wide | One `AGENTS.md` at root |

---

*See [Back Pressure](back-pressure.md) for the verification hierarchy this builds on, and [Validation Gates](validation-gates.md) for where evals fit in the development workflow.*

*Next: [PACK System](pack-system.md)*
