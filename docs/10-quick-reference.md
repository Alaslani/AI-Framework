# Quick Reference

> One-page cheat sheet for daily use.

---

## FIC Workflow

```
FIND (20%)        IMPLEMENT (70%)     COMPOUND (10%)
──────────        ───────────────     ──────────────
Research          Phase 1 → Verify    Capture learnings
Plan              Phase 2 → Verify    Update docs
Define success    Phase N → Verify    Apply to process
```

---

## Phase Checklist

### Research
- [ ] Understand current system
- [ ] Know affected files
- [ ] Identify patterns
- [ ] Document findings

### Planning
- [ ] Define success criteria
- [ ] Choose approach
- [ ] Break into phases
- [ ] Add verification steps

### Implementation
- [ ] Load minimal context
- [ ] Complete phase tasks
- [ ] Type check + lint + test
- [ ] Update progress

### Review
- [ ] Self-review checklist
- [ ] AI review
- [ ] Integration test
- [ ] Ready to merge

### Retrospective
- [ ] What worked
- [ ] What didn't
- [ ] Key learning (signal)
- [ ] Update knowledge base

---

## Context Rules

| Rule | Why |
|------|-----|
| Keep under 40% | Better output |
| One phase per session | Focused context |
| Clear between tasks | No pollution |
| Write to files | Persist state |

---

## Task Sizing

| Type | Research | Plan | Implement |
|------|----------|------|-----------|
| Quick (<50 lines) | 0-5m | 0m | 5-15m |
| Standard | 10-20m | 15-30m | 30-90m |
| Multi-phase | 30-60m | 30-60m | 2-4h |

---

## Verification Protocol

After every change:
```
1. Type check
2. Lint
3. Build
4. Test
```

---

## Thinking Keywords

| Keyword | Use For |
|---------|---------|
| "think" | Basic decisions |
| "think hard" | Architecture |
| "think harder" | Multi-system |
| "ultrathink" | Security/critical |

---

## Warning Signs

| Sign | Action |
|------|--------|
| AI repeating | Reset context |
| Can't explain output | Don't use it |
| Context full | Compact now |
| 3+ failed attempts | Different approach |

---

## Templates

| Template | When |
|----------|------|
| CLAUDE.md | Every project |
| feature-plan.md | New features |
| session-handoff.md | Between sessions |
| retrospective.md | After completion |

---

## Useful Prompts

### Research
```
"What files handle [X]?"
"Trace flow from [A] to [B]"
"What patterns exist for [X]?"
```

### Planning
```
"Plan implementation of [X]"
"What are options for [decision]?"
"Break into phases"
```

### Implementation
```
"Implement [task] following existing patterns"
"Add tests for [function]"
"Refactor [X] to use [pattern]"
```

### Review
```
"Review for bugs and edge cases"
"Check for security issues"
"What could break?"
```

---

## Verification Commands

```bash
# Type check
[your type check command]

# Lint
[your lint command]

# Build
[your build command]

# Test
[your test command]
```

---

*Version: 1.0*
