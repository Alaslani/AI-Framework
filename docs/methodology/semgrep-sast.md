# Semgrep SAST + /security-scan

Static analysis as a deterministic security gate for AI-assisted development.

## The Problem

[Back Pressure](back-pressure.md) ranks verification by how deterministic it is.
"Is this code secure?" asked of an LLM sits at the bottom of that pyramid — same
code, different prompt, different answer. Static Application Security Testing
(SAST) moves a whole class of security checks up the pyramid: injection sinks,
dangerous APIs, hardcoded secrets, and the [OWASP Top 10](https://owasp.org/www-project-top-ten/)
patterns the [Security Guide](../../SECURITY.md) describes can be detected by a
tool that returns the same result every time.

This guide shows how to add [Semgrep](https://semgrep.dev/) SAST to any project,
wire it into a `/security-scan` command Claude Code can run, and gate it in CI —
without the silent-pass failure modes that make a scan worse than no scan at all.

> A scan that passes because it scanned nothing is more dangerous than no scan.
> It converts "we don't know" into a false "we're clean." Most of this guide is
> about not doing that.

```
Local (fast feedback)            CI (enforcement)
┌──────────────────────┐         ┌──────────────────────────┐
│ /security-scan        │         │ Semgrep job on every PR  │
│  → discover src trees │  ───►   │  --severity ERROR        │
│  → semgrep --json     │         │  --error (gate)          │
│  → parse results[]    │         │  required check          │
│  + additive custom    │         │  (after baseline triage) │
└──────────────────────┘         └──────────────────────────┘
```

This is language-agnostic: the principles below apply whether the code is
Python, JavaScript/TypeScript, Go, Ruby, Java, or a polyglot monorepo.

---

## 1. Install Semgrep

Pick whichever fits the ecosystem. The local CLI and CI run the same engine, so
findings match between a developer's machine and the pipeline.

| Environment | Install | Notes |
|-------------|---------|-------|
| Python / any | `pipx install semgrep` | Recommended — isolated, no venv pollution |
| Python / any | `pip install semgrep` | Works in a virtualenv or CI image |
| macOS | `brew install semgrep` | Homebrew formula |
| Any (CI/local) | `docker run --rm -v "$(pwd):/src" semgrep/semgrep semgrep …` | No host install; mount the repo at `/src` |
| GitHub Actions | `semgrep/semgrep-action` (or run the container) | Official action, see CI section below |

Verify the install before relying on it:

```bash
command -v semgrep && semgrep --version
```

> **No account or token is required** to use the public rule registry (the
> `p/…` rulesets below). A token is only needed for the hosted Semgrep AppSec
> Platform (findings dashboard, Pro rules, diff-aware scans on their backend).
> Everything in this guide runs with the open-source CLI and public rules alone.

---

## 2. Pick Rulesets Per Language

Semgrep rulesets are referenced with `--config p/<name>`. Compose them: one or
two cross-cutting packs plus the language pack(s) for the code you actually have.

| Ruleset | Scope | Use when |
|---------|-------|----------|
| `p/owasp-top-ten` | Cross-language OWASP Top 10 | Almost always — the security baseline |
| `p/secrets` | Hardcoded credentials, keys, tokens | Almost always — pairs with the secrets scope |
| `p/security-audit` | Broad security smell coverage | Periodic deep scans (noisier) |
| `p/ci` | Curated, low-false-positive set | CI gating where signal-to-noise matters |
| `p/python` | Python-specific sinks and APIs | Python in the tree |
| `p/javascript` | JS-specific patterns | JS in the tree |
| `p/typescript` | TS-specific patterns | TS in the tree |
| `p/golang` | Go-specific patterns | Go in the tree |
| `p/java`, `p/ruby`, `p/csharp`, … | Per-language packs | That language in the tree |
| `p/django`, `p/react`, `p/express`, … | Framework packs | That framework in the tree |
| `p/docker`, `p/terraform`, … | IaC / container packs | Those files in the tree |

**Pick packs from what's actually in the tree, not from what the project
"is."** A "Python service" may include a JS dashboard and Terraform — discover
the languages present (see Principle 1) and add the matching packs. Compose like
this:

```bash
# Polyglot repo: cross-cutting packs + the languages that exist here
semgrep --config p/owasp-top-ten \
        --config p/secrets \
        --config p/python \
        --config p/typescript \
        <verified-source-paths>
```

Semgrep auto-detects file types within a pack, so adding `p/python` is harmless
if no Python is found — but adding a pack you never need just slows the scan.

---

## 3. The /security-scan Command

A `/security-scan` command gives Claude Code (and you) one entry point that
runs the right checks for the situation. Support these scopes:

| Scope | Question it answers |
|-------|---------------------|
| `full` | Everything — the default for a pre-merge or pre-deploy sweep |
| `sast` | What does static analysis find in our code? |
| `secrets` | Are there hardcoded credentials/keys/tokens? |
| `deps` | Do our dependencies have known vulnerabilities? |
| `code` | Project-specific code checks (custom rules + hand-rolled checks) |

The drop-in template lives at
[`templates/commands/security-scan.md`](../../templates/commands/security-scan.md) —
copy it to `.claude/commands/security-scan.md` in your project and adapt the
project-specific parts. The rest of this section is the design contract that
template implements.

### Scope → Steps Matrix

This matrix is the single source of truth. Every step the command runs must
trace to a cell here; a scope runs its row and nothing else.

| Step | `full` | `sast` | `secrets` | `deps` | `code` |
|------|:------:|:------:|:---------:|:------:|:------:|
| Discover & verify source trees | ✅ | ✅ | ✅ | ✅ | ✅ |
| Semgrep SAST (`p/owasp-top-ten` + lang packs) | ✅ | ✅ | | | |
| Semgrep secrets (`p/secrets`) | ✅ | | ✅ | | |
| Dependency audit (ecosystem auditor) | ✅ | | | ✅ | |
| Project-specific / custom checks (additive) | ✅ | | | | ✅ |

A single-purpose scope returns **only** that row's output. `/security-scan sast`
reports SAST findings — not dependency results, not a secrets summary. Mixing
extra checks into a narrow scope defeats the reason someone chose it.

---

## 4. Design Rules

These are the principles that separate a scan you can trust from one that lies
to you. The command template encodes them; state them explicitly so anyone
adapting it preserves them.

### Principle 1 — Scan the real source trees, never an assumed root

Discover where code actually lives, then verify each path exists before scanning
it. Semgrep over a path that doesn't exist finds nothing and **exits 0** — a
silent pass that reads as "clean."

```bash
# ❌ Assumes a layout. If ./src was renamed to ./packages, this passes empty.
semgrep --config p/owasp-top-ten ./src

# ✅ Discover, then verify, then scan only what exists
SRC_DIRS=()
for d in src app lib packages services cmd internal; do
  [ -d "$d" ] && SRC_DIRS+=("$d")
done
if [ ${#SRC_DIRS[@]} -eq 0 ]; then
  echo "ERROR: no known source directory found — refusing to report a clean scan"
  exit 2
fi
semgrep --config p/owasp-top-ten "${SRC_DIRS[@]}"
```

When in doubt, scanning the repo root (letting Semgrep walk it, respecting
`.semgrepignore`) is safer than guessing a subdirectory and missing the code.

### Principle 2 — Separate tool detection from result evaluation

Whether the tool *exists* and what the tool *found* are two different questions.
Conflating them is the most common bug in scan wrappers.

- **Detect** with `command -v semgrep` (or, in Claude Code, the presence of a
  Semgrep MCP plugin). Fall back to custom checks **only** when the tool is
  genuinely absent.
- **Evaluate** results from the run. With `--error`, Semgrep exits non-zero
  **because it found something** — that is a successful scan with findings, not
  a missing tool. Treating that exit code as "Semgrep unavailable" silently
  swaps real findings for a weaker fallback.

Prefer `--json` and parse `results[]`. An empty array is a clean result; a
populated array is findings. The exit code alone can't tell you which.

```bash
# ✅ Detection is separate from evaluation
if ! command -v semgrep >/dev/null 2>&1; then
  echo "Semgrep not installed — running custom checks only (reduced coverage)"
  run_custom_checks
else
  semgrep --config p/owasp-top-ten --json "${SRC_DIRS[@]}" > sast.json
  COUNT=$(jq '.results | length' sast.json)   # empty array = clean
  echo "Semgrep findings: $COUNT"
fi
```

### Principle 3 — The Scope → Steps matrix is the single source of truth

Gate every step against the matrix in Section 3. The command should branch on
scope and run exactly the matching row. This keeps single-purpose scopes pure:
`/security-scan secrets` runs the secrets row and reports secrets — no SAST
table, no dependency summary bolted on.

### Principle 4 — Custom checks run additively, not fallback-only

If Semgrep is the preferred path and the project still has hand-rolled checks
(a grep for a banned API, a regex for an internal secret format), do **not** make
those checks run only when Semgrep is missing. The checks Semgrep doesn't
reproduce must run on **every** scan, alongside Semgrep, and overlapping findings
get de-duplicated.

> Adding Semgrep must never reduce coverage. If a custom check caught something
> before and Semgrep doesn't catch it, that check still has to run.

```
❌ Fallback-only:  semgrep present? → run semgrep, skip custom checks
                   (coverage drops the moment you adopt semgrep)

✅ Additive:       run semgrep
                 + run the custom checks semgrep does NOT cover
                 → merge, de-dupe overlaps
```

Audit the overlap once: for each custom check, decide "Semgrep reproduces this"
(retire the custom check) or "Semgrep doesn't" (keep it, always-on). Don't leave
that decision implicit in control flow.

### Principle 5 — Sequence validation after the state it inspects exists

A step that inspects state must run after that state is produced. Verify a path
exists before scanning it (Principle 1). Build before scanning build output.
Install dependencies before auditing the dependency tree. A validation that runs
before its subject exists inspects nothing and passes — the same silent-pass
failure as scanning a missing path.

---

## 5. Add a CI Gate

Run Semgrep on every pull request and make it a required check — but roll it out
safely. Gating on day one against a repo that's never been scanned will either
block every PR or train everyone to ignore the check.

### Gate on ERROR severity

Use `--severity ERROR --error` so the job fails the build only on
ERROR-level findings. WARNING/INFO findings are reported but don't block — they
feed triage without halting delivery.

```bash
semgrep --config p/owasp-top-ten --config p/secrets \
        --severity ERROR --error \
        "${SRC_DIRS[@]}"
```

### Safe rollout sequence

1. **Assess the baseline first.** Run Semgrep across the repo (non-blocking) and
   count ERROR findings. You cannot choose a rollout path without this number.
2. **Zero ERROR findings → block immediately.** Add `--severity ERROR --error`,
   mark the job a required check. The repo stays clean from here.
3. **Non-zero ERROR findings → do not blanket-block.** Either:
   - **Triage and justify-suppress**: fix the real ones; for accepted/false
     positives add an inline `# nosemgrep: <rule-id>` with a reason, or list the
     rule in `.semgrepignore` / config. Then turn the gate on.
   - **Start non-blocking and ramp**: run the job reporting-only, drive the
     ERROR count to zero in batches, then flip to `--error` and require it.
4. **Never blanket-block a never-scanned repo.** A required ERROR gate is the
   *end* of rollout, not the start.

> This mirrors the calibration workflow in the [Evals System](evals-system.md):
> measure real counts first, fix in batches, promote to blocking when the count
> hits zero — never start at "everything is an error."

### Example GitHub Actions job

```yaml
# .github/workflows/semgrep.yml
name: Semgrep
on:
  pull_request:
    branches: [main]
jobs:
  sast:
    runs-on: ubuntu-latest
    container: semgrep/semgrep   # public registry rules need no token
    steps:
      - uses: actions/checkout@v4
      - name: Semgrep (gate on ERROR)
        run: |
          semgrep \
            --config p/owasp-top-ten \
            --config p/secrets \
            --severity ERROR --error
```

Once green, add this job to the branch's required status checks so a failing
scan blocks merge. Until the baseline is at zero ERRORs, run it **without**
`--error` (reporting-only) and require it only after triage.

---

## 6. Copy-Pasteable Snippets

Cross-cutting packs (`p/owasp-top-ten`, `p/secrets`) apply to every ecosystem;
add the language pack that matches your tree.

```bash
# Python
semgrep --config p/owasp-top-ten --config p/secrets --config p/python <src>

# JavaScript / TypeScript
semgrep --config p/owasp-top-ten --config p/secrets \
        --config p/javascript --config p/typescript <src>

# Go
semgrep --config p/owasp-top-ten --config p/secrets --config p/golang <src>

# Secrets only (the `secrets` scope)
semgrep --config p/secrets --json <src> | jq '.results | length'

# CI gate (any language): fail only on ERROR
semgrep --config p/owasp-top-ten --config p/secrets --severity ERROR --error <src>

# No local install: run via the official container
docker run --rm -v "$(pwd):/src" semgrep/semgrep \
  semgrep --config p/owasp-top-ten /src
```

Pair the SAST scan with the project's dependency auditor for the `deps` scope —
`pip-audit` / `npm audit` / `yarn audit` / `pnpm audit` / `govulncheck` /
`cargo audit` — chosen the same way you pick language packs: by what's in the
tree.

---

*See [Back Pressure](back-pressure.md) for the verification hierarchy SAST sits
in, the [Evals System](evals-system.md) for the calibrate-then-gate rollout
pattern, and the [Security Guide](../../SECURITY.md) for the vulnerability
classes these scans target.*

*Next: [Validation Gates](validation-gates.md)*
