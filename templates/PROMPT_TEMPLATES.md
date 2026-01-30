# Prompt Templates v1.0

> Clear prompts beat complex prompts. Tell the AI what TO do.

---

## Quick Reference

| Command | Use When | Thinking | Output |
|---------|----------|----------|--------|
| `/quick-task` | <50 lines, single file | — | Direct fix |
| `/research` | Need to understand first | "think" | research/[name].md |
| `/plan` | Multi-file, complex logic | "think hard" | plans/[name].md |
| `/implement` | Executing approved plan | — | Code changes |
| `/bug` | Report bug during work | — | Bug entry |
| `/compound` | Capture learnings | — | Knowledge update |

**Thinking Keywords**: `think` → `think hard` → `think harder` → `ultrathink`

---

## 1. Quick Task

```markdown
@[agent] [Title]

## Task
[One sentence description]

## File
[Exact path]

## Change
Before: [current behavior]
After: [desired behavior]
```

---

## 2. Research

```markdown
@[agent] /research [name]

## Questions
1. [Question about current state]
2. [Question about data flow]
3. [Question about dependencies]

## Output
Create `research/[name].md`

Think through this systematically.
```

---

## 3. Plan

```markdown
@[agent] /plan [name]

## Context
[Why this matters - 1-2 sentences]

## Requirements
1. [Must have]
2. [Must have]
3. [Nice to have]

## Constraints
- Must not break: [existing feature]
- Max phases: [3-5]
- Timeline: [deadline if any]

## Output
Create `plans/[name].md` with:
- Phase breakdown
- Files to modify
- Verification steps

Think hard about edge cases.
```

---

## 4. Implement

```markdown
@[agent] /implement [name] --phase [N]

## Plan Reference
`plans/[name].md`

## Phase [N] Goal
[One sentence from plan]

## Verification
After implementation:
1. Type check passes
2. Lint passes
3. Build succeeds
4. [Specific test]
```

---

## 5. Bug Report

```markdown
@[agent] /bug [title]

## Observed
[What happened]

## Expected
[What should happen]

## Steps to Reproduce
1. [Step 1]
2. [Step 2]

## Environment
[Production/Staging/Local]

## Severity
[Critical/High/Medium/Low]
```

---

## 6. Compound (Capture Learning)

```markdown
@[agent] /compound

## Session Summary
[What was accomplished]

## Learning
**Context**: [Why this came up]
**Wrong approach**: [What didn't work]
**Correct approach**: [What works]
**Why**: [Underlying reason]

## Update
Add to Master Reference learning #[next number]
```

---

## Agent Selection

```
API/Database work    → Backend specialist
UI/Components        → Frontend specialist
Tests                → QA specialist
Research/Docs        → Research specialist
Infrastructure       → DevOps specialist
Not sure?            → Start with /research
```

## Verification Checklist

After EVERY implementation:

```bash
# 1. Type check
[your type check command]

# 2. Lint
[your lint command]

# 3. Build
[your build command]

# 4. Test
[your test command or manual verification]
```

---

## Context Management

- Keep context under **40%** for best reasoning
- **Compact** when context grows large
- **Start fresh** between unrelated tasks
- Write progress to plan before context reset

---

*Version: 1.0*
