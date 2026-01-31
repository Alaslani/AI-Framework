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

## The Test

Ask yourself:

- Am I using process to avoid thinking?
- Am I skipping process to avoid work?

Both are wrong. Match the tool to the task.

---

*Frameworks fail from misuse, not missing features.*
