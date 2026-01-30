# Session Handoff

Transfer context between sessions without loss.

## The Problem

AI conversations don't persist. When you start a new session:
- Previous context is gone
- You repeat yourself
- Progress is lost
- Learnings disappear

## The Solution: Transfer Pack

A lightweight document (under 80 lines) that captures everything needed to continue.

## What to Include

### Essential (Always)
- Session ID and date
- What was completed
- Current state
- Immediate next steps

### Recommended (Usually)
- Files changed
- Key commits
- Blockers
- New learnings

### Optional (If Relevant)
- Task queue
- Open questions
- Dependencies

## Transfer Pack Structure

```markdown
# Transfer Pack v[X.X]

**Session**: [ID] | **Date**: [DATE] | **Status**: [BRIEF]

## Completed
- [Task 1] ✅
- [Task 2] ✅

## Current State
[2-3 sentences: what's working, what's not]

## Next Steps
1. [Immediate action]
2. [Following action]

## Files Changed
- `path/to/file.ts`

## Learnings
| # | Learning |
|---|----------|
| 1 | [New insight] |
```

## Session Naming Convention

Use: `<area>-<feature>`

Examples:
- `auth-oauth-flow`
- `api-rate-limiting`
- `ui-dark-mode`
- `infra-caching`

## Handoff Workflow

### Ending a Session

1. Create/update Transfer Pack
2. Commit current work
3. Note stopping point clearly

### Starting a Session

1. Read Transfer Pack
2. Read relevant MASTER_REFERENCE sections
3. Verify current state
4. Continue from next step

## Template

See: [templates/TRANSFER_PACK.md](../../templates/TRANSFER_PACK.md)

---

*Next: [Project Knowledge](project-knowledge.md)*
