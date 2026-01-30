# Phased Development

Break complex work into manageable phases.

## The Pyramid Principle

```
        /\
       /  \        Bad Research = 1000s of bad lines
      /    \
     /______\      Bad Plan = 100s of bad lines
    /        \
   /__________\    Bad Code = 1 bad line
```

**Key Insight:** Invest time at the top of the pyramid. Fixing research is cheap. Fixing code is expensive.

## Plans as Alignment Tools

Plans aren't just for the AI — they're for **team alignment**.

Why plans > code for communication:
- Plans are shorter than implementations
- Plans use natural language, not syntax
- Plans surface disagreements early
- Plans can be reviewed in minutes, not hours

> "I can't read 2,000 lines of code every day. But I can sure as heck read 200 lines of an implementation plan."
> — Dexter Horthy

When you approve a plan, you and the AI are on the same page. Disagreements surface before implementation, not after.

## Task Size Classification

| Type | Scope | Approach |
|------|-------|----------|
| **Quick Fix** | <50 lines, single file | Direct implementation |
| **Standard** | 1 session, clear scope | Brief plan → implement |
| **Multi-Phase** | 2+ sessions | Full planning, phase tracking |
| **Epic** | Multiple weeks | Break into milestones |

## Multi-Phase Workflow

### Phase Structure

Each phase should:
- Have a single clear goal
- Touch a bounded set of files
- Be independently verifiable
- Complete in one session

### Tracking

For multi-phase work:

1. **Create task** in your tracker
2. **Document phases** in plan
3. **Update status** after each phase
4. **Capture signal** (what you learned)

### Example Phase Breakdown

```
Feature: User Authentication

Phase 1: Database Schema
- Goal: Create user tables
- Files: migrations/, types/
- Verify: Migration runs successfully

Phase 2: API Routes
- Goal: Login/logout endpoints
- Files: api/auth/
- Verify: Endpoints return correct responses

Phase 3: UI Components
- Goal: Login form
- Files: components/auth/
- Verify: Form submits and redirects

Phase 4: Integration
- Goal: Connect everything
- Files: pages/, middleware
- Verify: Full flow works end-to-end
```

## When to Track vs Skip

### Track When:
- Multiple sessions required
- Multiple people involved
- Need to hand off to someone
- Complex with many phases

### Skip When:
- Quick fix (<30 minutes)
- Single session, clear scope
- Solo work, low complexity

## Context Management Between Phases

1. **End of phase**: Write progress to plan
2. **Start of next phase**: Read plan, continue
3. **Context full**: Compact and restart
4. **New session**: Use Transfer Pack

---

*Next: [Validation Gates](validation-gates.md)*
