# Project Knowledge

Persistent knowledge that grows with your project.

## The Problem

Learnings get lost:
- In chat history that expires
- In memory you forget
- In docs no one updates
- In context that resets

## The Solution: Master Reference

A single source of truth for your project that:
- Persists across sessions
- Grows with learnings
- Provides quick reference
- Enables handoff to others

## What Belongs in Master Reference

### Quick Reference (Top)
- Key URLs (repo, prod, staging)
- Important IDs
- Common commands
- Tech stack summary

### Architecture Overview
- System description (2-3 sentences)
- Tech stack table
- Key patterns used

### Decisions Log
- Numbered decisions
- Rationale for each
- Date made

### Learnings
- Numbered insights
- Categorized (gotcha, pattern, etc.)
- Context included

### External Integrations
- Third-party services
- API references
- Credentials location (not actual credentials)

### Cross-References
- Related documents
- Handoff files

## Update Cadence

| Event | Action |
|-------|--------|
| After each session | Add new learnings |
| After key decision | Log decision + rationale |
| After incident | Document what happened + fix |
| Weekly (optional) | Review and clean up |

## Anti-Patterns

❌ **Too long** — Keep scannable, link to details
❌ **Out of date** — Stale docs are worse than none
❌ **Duplicate info** — Single source of truth
❌ **No structure** — Use consistent sections

## Template

See: [templates/MASTER_REFERENCE.md](../../templates/MASTER_REFERENCE.md)

---

*Next: [Decision Logging](decision-logging.md)*
