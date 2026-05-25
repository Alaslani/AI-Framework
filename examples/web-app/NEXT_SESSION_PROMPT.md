# Next Session Prompt v2.4

**For**: anomaly-notification-ui | **Created**: 2026-01-30 | **Pairs with**: Transfer Pack v2.3

> Paste the **Session Start Prompt** below as the first message of the next session,
> after uploading the updated PACK docs (MASTER_REFERENCE v1.4, ROADMAP v2.1,
> TRANSFER_PACK v2.3).
>
> **Transfer Pack vs. Next Session Prompt** — the Transfer Pack describes where we left
> off (cost anomaly backend complete). This is the executable kickoff: the message to
> paste, the guardrails, and the one decision to settle before building the UI.

---

## Pre-Paste Snapshot

Confirm each item before pasting. If any ❌, fix it before starting the session.

| # | Check | Expected state |
|---|-------|----------------|
| 1 | Cost anomaly backend (Phases 1-2) | ✅ COMPLETE — do not re-implement the pipeline |
| 2 | `cost_anomalies` migration | ✅ Applied (`20260130_cost_anomalies.sql`) — do not edit schema |
| 3 | Type-check baseline | 0 errors on `main` after Phase 2 merge |
| 4 | First target this session | `AnomalyBanner.tsx` notification UI (P0, task #1) |
| 5 | Threshold decision (critical vs. warning) | ⏸️ UNRESOLVED — answer at session start |
| 6 | Hard rule | Reuse existing `NotificationProvider`; do not build a second notification system |

If all ✅ (and ⏸️ items are queued as the open question) → proceed.

---

## Session Start Prompt

```
anomaly-notification-ui start.

Received Transfer Pack v2.3.

Read order:
1. MASTER_REFERENCE v1.4
2. Transfer Pack v2.3
3. PROMPT_TEMPLATES v2.3
4. ROADMAP v2.1

Context: Last session completed the cost anomaly detection backend — hourly
pipeline, z-score detection (>2 SD from 30-day average), cost_anomalies table.
This session builds the notification UI (task #1, P0). Backend is frozen; we
consume it, we don't change it.

Open question that MUST be resolved before /plan:

  What is the threshold that separates a "critical" anomaly from a "warning"?
  (The banner's styling and whether it auto-shows depend on this.)

Possible answers and the branch each triggers:

  (a) "Critical = >3 SD, warning = 2-3 SD" → proceed to /plan for AnomalyBanner
      with two severity styles.
  (b) "Single severity for now, add tiers later" → proceed to /plan with one
      banner style; log tiering as a deferred ROADMAP item.
  (c) "Not sure — needs product input" → pause UI work; draft the question for
      product, pick up task #2 (Slack routing) which is unblocked.

DO NOT proceed to /plan or /implement until this is resolved. Do not assume a
default. Do not work against a guess.

Critical reminders for this session (do not violate):

  - Reuse the existing NotificationProvider from the alerts feature — do not
    build a parallel notification system.
  - Backend stays frozen: cost aggregator, anomaly-detector, and the
    cost_anomalies migration are not touched.
  - /research (if the alerts feature is unfamiliar) precedes /plan precedes
    /implement. Verify each phase: type-check > lint > build > test.
  - Store dismiss/snooze prefs in the existing user_settings table — no new
    table for UI state.

Output destination for this session's artifacts:
  plans/anomaly-notification-ui.md

Standing by for the answer to the open question.
```

---

## Expected Session-Start Response

The first response should:

1. Acknowledge receipt of Transfer Pack v2.3
2. Confirm Pre-Paste Snapshot items as visible in the uploaded docs
3. Ask the open question directly — "Critical vs. warning threshold — (a), (b), or (c)?"
4. Branch on the answer:
   - (a) → draft `plans/anomaly-notification-ui.md` with two severity styles
   - (b) → draft the plan with one style; add tiering to the ROADMAP deferred list
   - (c) → draft the product question; start task #2 (Slack routing) instead

Do not skip the open-question resolution. Do not assume a default. Do not draft
against a guess.

---

## Anti-Patterns to Avoid

| ❌ Don't | ✅ Do |
|----------|-------|
| Guess the critical/warning threshold and style the banner anyway | Ask first; halt the plan until resolved |
| Build a fresh notification context "because it's cleaner" | Reuse the existing `NotificationProvider` |
| "Improve" the backend pipeline while you're in there | Backend is frozen — consume it only |
| Add a new table for dismiss/snooze state | Use the existing `user_settings` table |
| Start `/implement` before the plan is reviewed | `/research` → `/plan` → `/implement`, verify each phase |

---

*Pairs with the Transfer Pack. The Transfer Pack is the memory; this is the ignition.*
