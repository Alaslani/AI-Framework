# Anti-Patterns

Common ways to misuse this framework. Avoid these.

---

## 1. Using FIC for Trivial Changes

**Wrong**: Running full Find → Implement → Compound for a typo fix.

**Right**: FIC is for multi-file features and unknowns. Quick fixes skip to implement.

| Task | Workflow |
|------|----------|
| Fix typo | Direct edit |
| Change button color | Direct edit |
| Add new API endpoint | FIC |
| Refactor auth flow | FIC |

---

## 2. MASTER_REFERENCE as a Changelog

**Wrong**: Adding every session's activities to MASTER_REFERENCE.

**Right**: Only add **learnings** — things that change future behavior.

| Add | Don't Add |
|-----|-----------|
| "RLS policies need SECURITY DEFINER for admin functions" | "Fixed bug in dashboard" |
| "Arabic numerals require toLocaleString('ar-SA')" | "Updated header component" |

---

## 3. TRANSFER_PACK as Chat Logs

**Wrong**: Copy-pasting conversation highlights into Transfer Pack.

**Right**: Synthesized handoff — what matters for the NEXT session.

**Bad Transfer Pack:**

```
We discussed the auth flow. Then we looked at the database.
Claude suggested using RLS. We tried it and it worked.
```

**Good Transfer Pack:**

```
## Completed
- Auth flow: RLS policies added to user_profiles, investments

## Blocked
- Payment integration waiting on payment provider contract

## Next Session
- Implement webhook handlers for payment confirmation
```

---

## 4. Skipping Verification Because "The Model Seems Confident"

**Wrong**: Shipping code because the AI said "this should work."

**Right**: Verify every phase. AI confidence ≠ correctness.

```bash
# After EVERY phase
npm run type-check
npm run lint
npm run build
npm run test
```

---

## 5. Context Hoarding

**Wrong**: Keeping a session alive for hours, hoping context helps.

**Right**: Fresh context with good handoff beats stale context every time.

**Reset when:**

- Same error 3+ times
- "I'll try a different approach"
- Context > 60%
- 10+ back-and-forth messages

---

## 6. Skipping Compound Because You're Tired

**Wrong**: Ending session without capturing learnings.

**Right**: 2 minutes of Compound saves 20 minutes next session.

If you're too tired to Compound, you're too tired to ship.

---

## 7. Over-Engineering Simple Tasks

**Wrong**: Creating research docs for config changes.

**Right**: Match effort to complexity.

| Complexity | Documentation |
|------------|---------------|
| Config change | None |
| Bug fix | Brief note in commit |
| New feature | Research + Plan |
| Architecture change | Full FIC + ADR |

---

## 8. Treating 40% as a Hard Rule

**Wrong**: Refusing to proceed at 41% context.

**Right**: 40% is a heuristic. Audits and migrations may need 60%+.

When exceeding 40%:

- Write explicit PROGRESS.md
- Plan for compaction
- Use fresh context for next phase

---

## 9. Implementing Against a Black Box Without Learning Tests

**Wrong**: Reading API docs and immediately writing an implementation plan.

**Right**: Run `/learn` first. Write tests that prove the API actually behaves
as documented.

| Step | Without /learn | With /learn |
|------|---------------|-------------|
| Plan | Based on docs (assumptions) | Based on proven behavior |
| Implementation | Discover surprises mid-code | Surprises caught before planning |
| Rework | Rewrite plan AND code | None — plan was correct |

See [Learning Tests](methodology/learning-tests.md).

---

## 10. Trusting LLM Self-Review as Verification

**Wrong**: Asking the AI "does this look correct?" and shipping when it says yes.

**Right**: Use deterministic checks. Type checkers don't change their answer
based on how you phrase the question.

| Ask | LLM Response |
|-----|-------------|
| "Is this code good?" | "Yes, well-structured." |
| "What's wrong with this?" | Lists 10 problems. |

Same model, same code, different steering. Use `tsc`, `pytest`, `cargo check`
instead.

See [Back Pressure Engineering](methodology/back-pressure.md).

---

## 11. Designing Back Pressure After Implementation

**Wrong**: Writing all the code first, then figuring out how to test it.

**Right**: Decide what CLI commands prove each phase works BEFORE writing code.

The harness is more important than the implementation. If you can't describe
how to verify a phase, you don't understand the phase well enough to build it.

---

## 12. System Prompt Bloat

**Wrong**: Putting every pattern, example, and reference table in CLAUDE.md "because it might be useful."

**Right**: Keep CLAUDE.md lean (~200-400 lines). Move detailed content to skill files loaded on demand.

Every token in CLAUDE.md is a tax on every conversation — even when the content is irrelevant.

| Keep in CLAUDE.md | Move to skill files |
|-------------------|---------------------|
| Tech stack, git conventions | Code patterns, examples |
| Verification commands | Reference tables |
| File structure overview | Testing templates |
| Key decisions | Design system, compliance details |

Reference: [Claude Code — Progressive Disclosure](integrations/claude-code.md#progressive-disclosure)

---

## 13. Tool Proliferation Without Audit

**Wrong**: Adding a new command or agent for every workflow, never removing old ones.

**Right**: Audit every 20-30 sessions. Merge overlapping, remove unused.

| Signal | Action |
|--------|--------|
| Two commands run same checks | Merge into one with flags |
| Multiple agents cover same domain | Consolidate into one |
| Command unused for a month | Remove — the model can improvise |

The Claude Code team maintains ~20 tools total. Every additional tool is cognitive load, not capability.

---

## 14. Constraining Language That Limits Model Adaptation

**Wrong**: Rigid "ALWAYS do X" rules for every task regardless of complexity.

**Right**: Scope instructions to context. Use "prefer" and "when [context]" instead of "always."

| ❌ Constraining | ✅ Adaptive |
|----------------|------------|
| "Always follow X workflow" | "For non-trivial features, prefer X" |
| "ALWAYS do Y" | "When doing Z, always Y" |
| Thinking keywords on every prompt | Let the model decide when to think deeper |

The Claude Code team learned this when TodoWrite reminders every 5 turns made Claude stick rigidly to the list instead of adapting. They replaced TodoWrite with Tasks — and removed the reminders. When models improve, old constraints can hurt rather than help.

---

## The Test

Ask yourself:

- Am I using process to avoid thinking?
- Am I skipping process to avoid work?

Both are wrong. Match the tool to the task.

---

## When NOT to Use This Framework

### Skip FIC For

| Scenario | Why |
|----------|-----|
| One-off scripts | No future sessions to hand off to |
| Throwaway experiments | Learning, not building |
| Hackathon spikes | Speed > sustainability (intentionally) |
| Tasks under 30 minutes | Overhead exceeds value |
| Solo exploration | You ARE the context |

### The Decision Rule

Ask: **"Will future-me or someone else need to understand this?"**

- Yes → Use framework
- No → Skip it

### Partial Adoption

You don't need all-or-nothing:

| Situation | Use |
|-----------|-----|
| Quick fix, want to remember why | Just Compound |
| Exploring unfamiliar code | Just Find |
| Know what to build, multi-step | Just Implement phases |
| Complex feature | Full FIC |

---

*Frameworks fail from misuse, not missing features.*
