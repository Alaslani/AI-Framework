# Decision Logging

Capture the why, not just the what.

## Signal Over Status

❌ **Bad:** "Status: Done"
✅ **Good:** "Status: Done | Signal: RLS bypass requires SECURITY DEFINER"

The status tells you something finished. The signal tells you what you learned.

> **Note**: "Signal over Status" is a documentation principle within this framework. It's distinct from the S2S (Spec to Signal) methodology, which describes the full development loop: Spec → Build → Deploy → Signal → Learn.

## Lesson Format

For each significant learning:

```markdown
### Learning #[N]: [Title]

**Context:** Why this came up
**Wrong approach:** What didn't work
**Correct approach:** What works
**Why:** The underlying reason
```

## Categories

### Gotchas
Silent failures, constraint violations, unexpected behaviors.

*Example:* "Foreign keys must reference auth.users, not user_profiles"

### Patterns
Successful approaches worth repeating.

*Example:* "Use SECURITY DEFINER for cross-table RLS policies"

### Anti-Patterns
Approaches that failed and why.

*Example:* "Don't use SELECT * in RLS policies—causes recursion"

### Decisions
Architectural choices with rationale.

*Example:* "Chose Supabase over Firebase for Postgres + RLS support"

## Numbering System

Number learnings sequentially across the project:

```markdown
## Learnings

| # | Learning | Category |
|---|----------|----------|
| 1 | FK must reference auth.users | Gotcha |
| 2 | Use SECURITY DEFINER for RLS | Pattern |
| 3 | Avoid SELECT * in policies | Anti-pattern |
```

Benefits:
- Easy to reference ("See learning #42")
- Shows project maturity
- Creates searchable history

## When to Log

- 🔴 Something broke unexpectedly
- 🟡 You spent >30 min debugging
- 🟢 You found a better approach
- 🔵 You made an architectural choice

## Integration with Master Reference

Add learnings to MASTER_REFERENCE.md in the Learnings section.

Reference in Transfer Packs: "New learning: see #47 in Master Reference"

---

*Next: [Memory Setup](../memory-setup.md)*
