# Phase 1: Research

> Understand before acting.

## Purpose

Research is the **highest leverage** phase. Bad research leads to thousands of bad lines of code. Time invested here saves 10x later.

---

## When to Research

| Situation | Research? |
|-----------|-----------|
| New codebase | Yes |
| Unfamiliar area | Yes |
| Complex feature | Yes |
| Bug in unknown code | Yes |
| Quick fix in known area | No |

---

## Workflow

```
1. SCOPE    → What do I need to understand?
2. SCAN     → What files exist? What's the structure?
3. TRACE    → How does data flow? What calls what?
4. DOCUMENT → Write findings (~200 lines max)
5. VERIFY   → Is my understanding correct?
```

---

## Research Questions

### For Features
- Where does similar functionality exist?
- What patterns does this codebase use?
- What will this feature touch?
- Are there existing utilities to reuse?

### For Bugs
- What's the expected behavior?
- What's the actual behavior?
- When did it start failing?
- What changed recently?

### For Refactoring
- What depends on this code?
- What are the test coverage gaps?
- What's the minimal change needed?

---

## Output Format

```markdown
# Research: [Topic]

## Context
[Why this research - 2-3 sentences]

## Findings

### Structure
[Key files and purposes]

### Data Flow
[How data moves through system]

### Patterns
[Existing patterns to follow]

## Key Files
| File | Purpose | Relevance |
|------|---------|-----------|
| ... | ... | High/Med/Low |

## Open Questions
[Unresolved items]

## Recommendation
[Proposed approach]
```

---

## Useful Commands

```
"What files handle [X]?"
"Trace the flow from [A] to [B]"
"What does [function] do?"
"Find all usages of [X]"
"What patterns exist for [X]?"
```

---

## Checkpoint

Before proceeding to planning:

- [ ] Can explain the current system
- [ ] Know what files will be affected
- [ ] Understand existing patterns
- [ ] Questions answered

---

## Time Investment

| Complexity | Research Time |
|------------|---------------|
| Quick fix | 0-5 min |
| Standard | 10-20 min |
| Multi-phase | 30-60 min |
| Major | 1-2 hours |

Research = ~20% of total task time.

---

## Anti-Patterns

| Anti-Pattern | Result | Fix |
|--------------|--------|-----|
| Skip research | Wrong approach | Always research unfamiliar |
| Endless research | Paralysis | Time-box to 200 lines |
| Shallow scan | Missing impacts | Trace data flow |

---

## Next

When research is complete → [Planning Phase](03-planning-phase.md)
