# Prompt Templates

Command reference for AI-assisted development.

## Quick Reference

| Command | Use When | Thinking | Output |
|---------|----------|----------|--------|
| `/quick-task` | <50 lines, single file | — | Direct fix |
| `/research` | Need to understand first | "think" | research/[name].md |
| `/plan` | Multi-file, complex | "think hard" | plans/[name].md |
| `/implement` | Executing approved plan | — | Code changes |
| `/bug` | Report bug during work | — | Bug entry |
| `/compound` | Capture learnings | — | Knowledge update |

## Thinking Keywords

| Keyword | Use Case |
|---------|----------|
| "think" | Basic reasoning |
| "think hard" | Complex logic, architecture |
| "think harder" | Multi-system changes |
| "ultrathink" | Security, payments, critical |

## Agent Selection

```
API/Database     → Backend specialist
UI/Components    → Frontend specialist
Tests            → QA specialist
Research/Docs    → Research specialist
Infrastructure   → DevOps specialist
Not sure?        → Start with /research
```

## Template Reference

See: [templates/PROMPT_TEMPLATES.md](../../templates/PROMPT_TEMPLATES.md) for full templates.

## Workflow Integration

### Quick Task

For simple fixes:

```markdown
@[agent] [Title]

## Task
[One sentence]

## File
[Exact path]

## Change
Before: [current]
After: [desired]
```

### Research

Before complex work:

```markdown
@[agent] /research [name]

## Questions
1. [Current state?]
2. [Data flow?]
3. [Dependencies?]

## Output
Create research/[name].md
```

### Plan

For multi-phase work:

```markdown
@[agent] /plan [name]

## Context
[Why this matters]

## Requirements
1. [Must have]
2. [Must have]

## Constraints
- Must not break: [feature]
- Max phases: [N]
```

### Implement

Execute approved plan:

```markdown
@[agent] /implement [name] --phase [N]

## Plan Reference
plans/[name].md

## Phase [N] Goal
[From plan]

## After
[Verification commands]
```

## Verification Checklist

After every implementation:

```bash
# 1. Type check
[your command]

# 2. Lint
[your command]

# 3. Build
[your command]

# 4. Test
[your command]
```

---

*Back to: [Overview](../overview.md)*
