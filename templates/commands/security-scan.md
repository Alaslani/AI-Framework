---
description: "Run security scans (SAST, secrets, deps, custom) with Semgrep — scoped, additive, and honest about coverage"
argument-hint: "[full|sast|secrets|deps|code]"
---

# Security Scan: $ARGUMENTS

> Drop-in template. Copy to `.claude/commands/security-scan.md` in your project.
> Fill the `[bracketed]` parts with your project's real paths and checks; keep
> the design rules intact. Companion guide:
> `docs/methodology/semgrep-sast.md` in AI-Framework.

## Role

You are a security engineer running a static-analysis sweep. You report findings
honestly — including "I could not scan X." You never report a clean result you
did not actually earn. A scan that passes because it scanned nothing is a
failure, not a pass.

## Input Parsing

- `$ARGUMENTS` = the scope. One of: `full` (default if empty), `sast`,
  `secrets`, `deps`, `code`.
- Run **only** the steps in this scope's column of the Scope → Steps matrix.
  A single-purpose scope returns only that tool's output — nothing else.

### Scope → Steps Matrix (single source of truth)

| Step | `full` | `sast` | `secrets` | `deps` | `code` |
|------|:------:|:------:|:---------:|:------:|:------:|
| 1. Discover & verify source trees | ✅ | ✅ | ✅ | ✅ | ✅ |
| 2. Semgrep SAST (OWASP + lang packs) | ✅ | ✅ | | | |
| 3. Semgrep secrets (`p/secrets`) | ✅ | | ✅ | | |
| 4. Dependency audit | ✅ | | | ✅ | |
| 5. Custom / project-specific checks | ✅ | | | | ✅ |

Gate every action against this matrix. If a step is not checked for the active
scope, do not run it and do not mention its results.

## Steps

### Step 1: Discover & verify source trees (every scope)

Find where code actually lives — never assume a root. Check the candidates and
keep only the paths that exist:

```bash
SRC_DIRS=()
for d in [src app lib packages services cmd internal]; do
  [ -d "$d" ] && SRC_DIRS+=("$d")
done
```

- If **no** source directory is found, STOP. Do not run a scan over a missing
  path (it exits 0 and falsely reads as clean). Report the problem and ask where
  the code lives.
- Detect which languages are present so you can pick matching rule packs
  (`p/python`, `p/typescript`, `p/golang`, …).

### Step 2: Semgrep SAST — scopes `full`, `sast`

First **detect the tool** (separate from evaluating results):

```bash
command -v semgrep   # or: confirm a Semgrep MCP plugin is available
```

- **Tool present** → run it and parse results. Use `--json` and read
  `results[]`. An empty array is clean; a populated array is findings. A
  non-zero exit from `--error` means **findings were found**, NOT that the tool
  is unavailable — never downgrade to fallback on that exit code.

  ```bash
  semgrep --config p/owasp-top-ten [--config p/python --config p/typescript] \
          --json "${SRC_DIRS[@]}" > /tmp/sast.json
  jq '.results | length' /tmp/sast.json   # 0 = clean
  ```

- **Tool genuinely absent** → say so explicitly ("reduced coverage"), then run
  the custom checks from Step 5 that cover SAST-class issues.

### Step 3: Semgrep secrets — scopes `full`, `secrets`

```bash
semgrep --config p/secrets --json "${SRC_DIRS[@]}" | jq '.results | length'
```

Report hardcoded credentials, keys, and tokens. For the `secrets` scope, report
only this — no SAST table, no dependency summary.

### Step 4: Dependency audit — scopes `full`, `deps`

Run the auditor for the ecosystem(s) detected in Step 1. Ensure dependencies are
installed/resolved **before** auditing (sequence the check after the state it
inspects exists):

```bash
[ pip-audit | npm audit | yarn audit | pnpm audit | govulncheck ./... | cargo audit ]
```

### Step 5: Custom / project-specific checks — scopes `full`, `code`

Run the project's hand-rolled checks **additively**, not as a fallback:

- These run on every `full`/`code` scan, alongside Semgrep — not only when
  Semgrep is missing.
- Run the checks Semgrep does **not** reproduce. De-duplicate anything that
  overlaps with a Semgrep finding so it isn't reported twice.
- Adding Semgrep must never reduce coverage. If a custom check caught something
  before, it still runs.

```bash
# [Examples — replace with your project's checks:]
# grep -rn "[banned_api(]" "${SRC_DIRS[@]}"
# grep -rnE "[INTERNAL_TOKEN_[A-Z0-9]{16}]" "${SRC_DIRS[@]}"
```

## Guardrails

- NEVER scan an assumed/hardcoded path without verifying it exists — a scan over
  a missing path passes silently and gives false assurance.
- NEVER treat Semgrep's "findings found" exit code (non-zero with `--error`) as
  "tool unavailable." Detect absence with `command -v`; evaluate results from
  `results[]`.
- NEVER mix steps from other scopes into a single-purpose scope.
- NEVER make custom checks fallback-only — they run additively on `full`/`code`.
- NEVER run a validation step before the state it inspects exists (verify paths
  before scanning; install deps before auditing; build before scanning output).
- NEVER report "clean" when a step was skipped, a tool was missing, or a path
  was absent. Say what was and wasn't scanned.

## Output Format

```
## Security Scan — scope: <scope>

Scanned paths: <verified source trees>     Languages: <detected>
Semgrep: <version | NOT INSTALLED — reduced coverage>

<Only the sections this scope's matrix row enables:>

### SAST (Semgrep)
<N findings  |  clean (results[] empty)>
- <rule-id> · <severity> · <file:line> — <message>

### Secrets (Semgrep p/secrets)
<N findings | clean>

### Dependencies
<auditor output summary>

### Custom checks
<additive findings Semgrep did not cover; overlaps de-duped>

### Skipped / could not scan
<anything not run, and why — never silently omit>
```

## Failure Modes

- If no source tree is found → STOP, report it, ask where the code lives. Do not
  emit a clean result.
- If Semgrep is not installed → state "reduced coverage," run additive custom
  checks, and label the SAST section accordingly.
- If a scope is unrecognized → default to `full` and note the assumption.
- If a step's prerequisite state doesn't exist yet (e.g., deps not installed) →
  produce it first, or report the step as skipped — never run it against nothing.
