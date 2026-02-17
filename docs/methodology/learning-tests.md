# Learning Tests

Prove how external systems actually behave before planning around assumptions.

## The Problem

Documentation lies. SDKs have undocumented behavior. APIs have edge cases
that only surface at runtime. If you plan an implementation around assumptions
about a black-box system and those assumptions are wrong, you rewrite the plan
AND the code.

Learning tests catch wrong assumptions before they become wrong implementations.

## What is a Learning Test?

A test that verifies how an external system you can't read the source of
actually behaves. Not a unit test (you own the code). Not an integration test
(tests your code against theirs). A learning test tests THEIR code in isolation.

Origin: Michael Feathers, "Working Effectively with Legacy Code"

> "Hello World is the most generic version of a learning test."

Every developer does this informally — `curl` an endpoint, print a response,
try an edge case in a REPL. Learning tests formalize this into reusable,
re-runnable artifacts.

## When to Use

| Scenario | Use /learn? | Why |
|----------|-------------|-----|
| Third-party payment SDK | Yes | Can't read source, subtle edge cases |
| Government identity API | Yes | No source access, undocumented behaviors |
| CLI wrapping a closed binary | Yes | Behavior != documented behavior |
| Your own database | No | You own it, can read the schema |
| Well-documented framework (Next.js, Rails, Django) | No | Deterministic, well-documented |
| UI/UX design decisions | No | Design problem, not a black-box |

**Decision rule**: Can you read the source code? No → Learning test. Yes → Skip.

## The /learn Command

Sits between /research and /plan in the FIC workflow:

```
/research → /learn → /plan → /implement
   ↑                            |
   └── if assumption wrong ─────┘
```

### Input

```markdown
/learn [integration-name]

## External System
[SDK/API name + version]

## Documentation
[Link to docs or local file path]

## Assumptions to Verify
1. [What we think happens when X]
2. [Expected response shape for Y]
3. [Edge case: timeout/error/invalid input]

## Output
Create `docs/[name]/LEARNING_TESTS.md`
```

### Output Format

````markdown
# [Integration] Learning Tests

## Key Findings (Updated after running)
1. ✅ Assumption 1 confirmed: [actual behavior]
2. ❌ Assumption 2 FALSE: Expected X, got Y — plan must account for this
3. ⚠️ Assumption 3 partial: Works for case A but not case B

## Test: [Assumption Name]
```[language]
// What we're testing: Does the API return X when we send Y?
const result = await sdk.doThing({ param: "value" });

// Expected: { status: "success", data: [...] }
// Actual: { status: "success", data: [...] } ✅
```

## Test: [Edge Case Name]
```[language]
// What we're testing: What happens on timeout?
// Expected: Throws TimeoutError
// Actual: Returns { error: "ETIMEOUT" } — does NOT throw! ❌
try {
  const result = await sdk.doThing({ timeout: 1 });
  console.log("No throw, got:", result); // This is what actually happens
} catch (e) {
  console.log("Threw:", e.message);
}
```
````

The Key Findings section is **updated after running**, not written speculatively.

## The Critical Rule

**Do NOT proceed from /learn to /plan if any assumptions are marked ❌.**

Fix the assumption first. Re-run. Then plan based on proven behavior, not hoped-for behavior.

## Learning Tests as Contract Tests

Learning tests aren't throwaway. They become **contract tests** — re-runnable
verification that your external dependencies still behave as expected.

Store in: `tests/contracts/` or `docs/[name]/learning-tests/`

Re-run when:
- External SDK version bumps
- "Something changed" but you're not sure what
- Starting a new phase of integration work
- External provider announces API changes

These are NOT run in CI. They're manual, on-demand verification that your
external contract still holds.

---

*Next: [Back Pressure](back-pressure.md)*
