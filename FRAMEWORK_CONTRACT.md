# Framework Contract

This document defines the minimum requirements for correct usage of the AI Framework.

Compliance is binary: you either follow the contract or you don't.

---

## 1. Mandatory Artifacts

| Artifact | Required | Purpose |
|----------|----------|---------|
| MASTER_REFERENCE.md | Yes | Long-term project knowledge |
| TRANSFER_PACK.md | Yes (per session) | Context handoff between sessions |
| PROGRESS.md | Conditional | Mid-feature checkpoint (multi-phase work) |
| ROADMAP.md | Recommended | Priority tracking and timeline |

---

## 2. FIC Phase Outputs

Each phase must produce specific outputs. This makes FIC auditable.

### Find Phase

Must produce at least one of:

- Written understanding of system/files
- Identified risks or constraints
- Explicit assumptions documented

### Implement Phase

Must include:

- A plan (max 3–5 phases)
- Verification after each phase
- Progress updates for multi-phase work

### Compound Phase

Must produce:

- At least one captured learning
- Update to MASTER_REFERENCE if knowledge is reusable
- Transfer Pack if ending session

---

## 3. Verification Rules

**Non-negotiable.**

| Rule | Requirement |
|------|-------------|
| Timing | Verification is required after every Implement phase |
| Skipping | Skipping verification is a framework violation |
| Failures | Must be fixed or documented before continuing |
| Minimum | Type check, lint, build (test if available) |

No exceptions.

---

## 4. Context Discipline Rules

| Condition | Action |
|-----------|--------|
| Context > 60% | Requires compaction or reset |
| Same error 3+ times | Requires context reset |
| "I'll try a different approach" | Context is polluted — reset |
| Transfer Pack | Must summarize, not log conversations |

These are rules, not suggestions.

---

## 5. Out of Scope

The framework explicitly does NOT:

| Exclusion | Reason |
|-----------|--------|
| Mandate specific tools | Tool-agnostic by design |
| Automate decisions | Human judgment required |
| Replace verification | AI confidence ≠ correctness |
| Optimize for speed over correctness | Quality is non-negotiable |
| Require CLI or packages | Zero dependencies |

---

## 6. Compliance Definition

A project is considered **compliant** with this framework if:

1. All mandatory artifacts exist
2. FIC phase outputs are produced
3. Verification rules are followed
4. Context discipline is maintained

---

## 7. Violations

Common violations and their impact:

| Violation | Impact |
|-----------|--------|
| No TRANSFER_PACK at session end | Knowledge loss, repeated work |
| Skipped verification | Bugs ship to production |
| MASTER_REFERENCE never updated | Learnings don't compound |
| Context hoarding (>60%) | Degraded AI reasoning |
| Compound phase skipped | Technical debt invisible |

---

## Why This Contract Exists

| Without Contract | With Contract |
|------------------|---------------|
| "Here's how I like to work" | "Here is the system we agreed to use" |
| Inconsistent adoption | Clear expectations |
| Deviation is accidental | Deviation is intentional |
| Hard to review | Auditable compliance |

---

*This contract does not make the framework rigid. It reduces bikeshedding, increases autonomy inside clear boundaries, and makes deviations intentional instead of accidental.*
