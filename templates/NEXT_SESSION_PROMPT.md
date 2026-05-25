# Next Session Prompt v[X.X]

**For**: [NEXT SESSION ID] | **Created**: [DATE] | **Pairs with**: Transfer Pack v[X.X]

> Paste the **Session Start Prompt** below as the first message of the next session,
> after uploading the updated PACK docs ([MASTER_REFERENCE v[X.X], ROADMAP v[X.X],
> TRANSFER_PACK v[X.X]]).
>
> **Transfer Pack vs. Next Session Prompt** — the Transfer Pack *describes* where you
> left off (state). This doc is *executable*: the exact message to paste, the guardrails
> to enforce, and the one decision that must be made before any work starts. Write it at
> session end while the context is still in your head.

---

## Pre-Paste Snapshot

Confirm each item before pasting. If any ❌, fix it before starting the session.

| # | Check | Expected state |
|---|-------|----------------|
| 1 | [Closed track / frozen area] | ✅ [e.g. CLOSED last session — do not re-investigate] |
| 2 | [Baseline that must not regress] | [e.g. type-check baseline = N] |
| 3 | [Decided methodology / boundary] | ✅ [e.g. one page = one PR] |
| 4 | [First target for this session] | [e.g. route / file / feature] |
| 5 | [Unresolved input needed from you] | ⏸️ UNRESOLVED — answer at session start |
| 6 | [Hard rule that constrains the loop] | [e.g. never start on high-coupling surface X] |

If all ✅ (and ⏸️ items are queued as the open question) → proceed.

---

## Session Start Prompt

```
[Session ID] start.

Received Transfer Pack v[X.X].

Read order:
1. MASTER_REFERENCE v[X.X]
2. Transfer Pack v[X.X]
3. PROMPT_TEMPLATES v[X.X]
4. ROADMAP v[X.X]

Context: [1-3 sentences — what last session decided/produced, and what
this session is meant to do. Carry forward only what changes behavior.]

Open question that MUST be resolved before any /research or /plan:

  [The single decision blocking the next step.]

Possible answers and the branch each triggers:

  (a) [Answer A] → [next action, e.g. proceed to /research with X as the source]
  (b) [Answer B] → [next action, e.g. pause and resolve dependency Y first]
  (c) [Answer C] → [next action, e.g. dispatch / redirect to fallback target]

DO NOT proceed to /research, /plan, or /implement until this is resolved.
Do not assume a default. Do not work against a guess.

Critical reminders for this session (do not violate):

  - [Hard rule 1 — e.g. one unit of work = one PR]
  - [Hard rule 2 — e.g. frozen area X stays untouched]
  - [Hard rule 3 — e.g. /research precedes /plan precedes /implement; review at each gate]
  - [Hard rule 4 — e.g. verification: type-check > lint > build > test after each phase]
  - [Project-specific guardrail carried from a prior learning]

Output destination for this session's artifacts:
  [path/to/output.md]

Standing by for the answer to the open question.
```

---

## Expected Session-Start Response

The first response should:

1. Acknowledge receipt of Transfer Pack v[X.X]
2. Confirm Pre-Paste Snapshot items as visible in the uploaded docs
3. Ask the open question directly — "[restate the (a)/(b)/(c) choice]?"
4. Branch on the answer:
   - (a) → [first concrete action]
   - (b) → [first concrete action]
   - (c) → [first concrete action]

Do not skip the open-question resolution. Do not assume a default. Do not
draft against a guess.

---

## Anti-Patterns to Avoid

| ❌ Don't | ✅ Do |
|----------|-------|
| Assume the answer to the open question | Ask first; halt until resolved |
| Draft `/research` or `/plan` from memory of "what it should be" | Halt until the real source artifact is identified |
| Bundle separate units of work "since they're related" | Keep the agreed work boundary (one unit = one PR) |
| Start on the hardest / highest-coupling surface "because it matters most" | Follow the agreed sequencing rule |
| Skip review on a gate because "it's simple" | Apply the workflow's hard rules even to easy work |
| Re-open a closed track or touch a frozen area | Respect the snapshot — closed stays closed |

---

*Pairs with the Transfer Pack. The Transfer Pack is the memory; this is the ignition.*
