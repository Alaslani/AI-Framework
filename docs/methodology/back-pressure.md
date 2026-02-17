# Back Pressure Engineering

Deterministic feedback loops for AI-assisted development.

## The Core Principle

> "You can accidentally steer a model. You cannot accidentally steer a type checker."

When AI writes code, it needs ways to verify its own work. The more
deterministic those verification methods are, the more reliable the output.

Back pressure = automated feedback that pushes back against wrong code before
a human ever sees it.

## The Back Pressure Pyramid

```
Most Deterministic (Best)
┌─────────────────────────────────────────────┐
│  Type Checker    tsc, mypy, cargo check     │  ← Catches type errors
├─────────────────────────────────────────────┤
│  Build           next build, cargo build    │  ← Catches compilation errors
├─────────────────────────────────────────────┤
│  Unit Tests      vitest, pytest, cargo test │  ← Catches logic errors
├─────────────────────────────────────────────┤
│  Integration     E2E, API contract tests    │  ← Catches contract errors
├─────────────────────────────────────────────┤
│  Learning Tests  External system probes     │  ← Catches assumption errors
├─────────────────────────────────────────────┤
│  Visual/Manual   Screenshots, click-through │  ← Catches UI/UX errors
├─────────────────────────────────────────────┤
│  LLM Review      "Does this look right?"    │  ← Weakest — can be steered
└─────────────────────────────────────────────┘
Least Deterministic (Weakest)
```

Each layer catches a different class of error. The higher in the pyramid,
the more trustworthy the signal.

## Five Rules

### 1. Prefer Deterministic Over LLM-Based Review

A type checker says "wrong" or "right." An LLM says "looks good to me"
and then finds 10 problems when asked differently.

Same model, same code, different prompt → different answer. Type checkers
don't have this problem.

### 2. Back Pressure Must Be Observable

The AI needs the output. If a test fails silently, it's not back pressure.
The model needs tokens back — stdout, test results, error messages — to
correct course.

### 3. Back Pressure Doesn't Need to Be Binary

Logs, response shapes, and partial results all count. A test that prints
the actual API response shape is valuable even if it doesn't assert pass/fail.

### 4. Invest in Harness Before Code

The best AI engineers spend more time designing verification than
implementation. The harness is the product. The code is generated from it.

This mirrors the Review Hierarchy from [Phased Development](phased-development.md):
research and plans prevent more bad code than code review does.

### 5. Every Phase Must Specify Its Back Pressure

When writing an implementation plan, each phase should answer:
"What CLI commands prove this phase works?"

Bad: "Phase 2: Add payment webhook handler"
Good: "Phase 2: Add payment webhook handler — verify: type-check passes,
unit test for valid/invalid signatures, manual curl with test payload"

## Why LLM-as-Judge is Overrated

| Prompt | Response |
|--------|----------|
| "Is this code good?" | "Yes, this looks well-structured." |
| "What's wrong with this code?" | Lists 10 problems. |

Same model. Same code. Different steering. This is why LLM review sits at
the bottom of the pyramid — it tells you what you steer it toward.

Deterministic tools don't have this problem. A type checker either passes
or it doesn't. A test either passes or it doesn't.

## The Mandatory Pattern

After every implementation phase:

```bash
# 1. Types correct?
[type-check command]    # tsc --noEmit, mypy, cargo check, etc.

# 2. Compiles?
[build command]         # next build, cargo build, go build, etc.

# 3. Logic correct?
[test command]          # vitest, pytest, cargo test, etc.
```

This is the minimum. Projects should add layers as they mature.

## Auditing Your Back Pressure

Use this template to inventory what feedback loops your project has:

| Layer | Tool | What It Catches | Status |
|-------|------|-----------------|--------|
| Types | [your type checker] | Wrong signatures, missing props | ✅/❌ |
| Build | [your build tool] | Invalid imports, SSR issues | ✅/❌ |
| Unit | [your test runner] | Logic regressions | ✅/❌ |
| Integration | [E2E framework] | Cross-module contract breaks | ✅/❌ |
| Learning | Contract tests | External API behavior changes | ✅/❌ |
| Visual | [screenshots/manual] | Layout, responsive, a11y | ✅/❌ |
| Pre-commit | [hooks/linters] | Style, import boundaries | ✅/❌ |

Gaps in this table are opportunities, not failures. Fill them over time,
starting from the top of the pyramid.

### Visual Architecture Diagrams

Auto-generated dependency diagrams are an underused form of deterministic back pressure. When an AI agent accidentally introduces a cross-boundary import or creates a circular dependency, a diagram generated from the actual codebase makes the violation immediately visible.

**Why this works as back pressure:**

- The diagram is generated from code, not from LLM judgment — it can't be steered
- Structural violations that are invisible in diffs become obvious in visual form
- The agent can compare before/after diagrams to verify its changes didn't break architecture boundaries

**When to invest in this:**

| Signal | Action |
|--------|--------|
| Monorepo with multiple packages or modules | Generate cross-package dependency graph |
| Strict layering (e.g., UI → API → DB) | Visualize layer violations |
| Agent keeps introducing circular imports | Add diagram generation to phase verification |
| Code review repeatedly catches architecture drift | Automate what reviewers are checking visually |

**Implementation approaches:**

- Language-native tools: `madge` (JS/TS), `pydeps` (Python), `cargo-depgraph` (Rust), `go mod graph` (Go)
- Generic: `dependency-cruiser` for JS/TS with rule-based violation detection
- Custom: parse imports and generate Mermaid/Graphviz diagrams in CI

The key insight is that dependency diagrams sit between "Integration Tests" and "Visual/Manual" on the back pressure pyramid — they're more deterministic than screenshots but less deterministic than unit tests. They catch a class of errors (structural/architectural violations) that no other layer in the pyramid reliably catches.

---

*Next: [PACK System](pack-system.md)*
