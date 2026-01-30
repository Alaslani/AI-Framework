# Prompt Templates v1.1

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

## 0. Context Engineering Principles

> Based on Dexter Horthy's "Advanced Context Engineering for Agents"

### 40% Rule

Keep context utilization **under 40%** for best results.

```
Context Budget = ~170,000 tokens
Target Usage   = <40% (~68,000 tokens)
Why?           = More room for work = better outputs
```

**Note**: This is a heuristic, not a hard limit. For large migrations or audits, exceed 40% but use compaction strategies and explicit progress files.

**If context > 60%**: Stop → Compact → New context window

### Review Hierarchy

| Level | Bad Output = | Review Priority |
|-------|--------------|-----------------|
| Research | 1000s bad lines of code | ⭐⭐⭐ Highest |
| Plan | 100s bad lines of code | ⭐⭐ High |
| Code | 1 bad line of code | ⭐ Normal |

**Insight**: If you're shouting at the AI, the plan was bad.

### Fresh Context Triggers

Start new context window when you see:
- "I'll try a different approach"
- "Let me reconsider"
- Agent loops on same error 3+ times
- Context utilization > 60%
- Back-and-forth for 10+ messages

### Intentional Compaction

❌ Don't use automatic `/compact` — it loses important context

✅ Do write explicit progress files:
- PROGRESS.md (during feature work)
- Transfer Pack (end of session)
- Update plan with "✅ Done" markers

### Spec-First Development

The spec is more important than the code.

```
Prompts + Specs = Source code
Generated code  = Compiled artifact
```

If AI writes more code, specs become the thing you maintain.

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
Create `research/[name].md` with:
- File paths WITH line numbers (e.g., `src/lib/auth.ts:45-67`)
- Data flow diagrams
- Key functions/components identified
- Entry points for implementation

Think through this systematically.
```

### Research Output Format

Good research enables implementation without re-searching:

```markdown
## File Map
| File | Lines | Purpose |
|------|-------|---------|
| `src/lib/auth.ts` | 45-67 | Token validation |
| `src/app/api/login/route.ts` | 12-34 | Login endpoint |

## Data Flow
User → LoginForm → /api/login → auth.ts:validateToken() → DB

## Key Functions
- `validateToken()` at auth.ts:45 — does X
- `createSession()` at auth.ts:89 — does Y
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
- Files to modify (with line numbers)
- Verification steps per phase

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

## 7. Agent Selection

```
API/Database work    → Backend specialist
UI/Components        → Frontend specialist
Tests                → QA specialist
Research/Docs        → Research specialist
Infrastructure       → DevOps specialist
Not sure?            → Start with /research
```

**Subagent Purpose**: Context control, NOT role-play. Use subagents to offload search/find tasks so the parent agent stays focused. See [Subagent Usage](../docs/patterns/subagent-usage.md).

---

## 8. Common Mistakes

| ❌ Don't | ✅ Do |
|----------|-------|
| "Fix the bug" | "Error X in file Y, expected Z" |
| Start with /implement | Start with /research |
| Long prompts | Short prompt + one example |
| Use /compact | Write explicit progress files |
| Keep going when context > 60% | Compact and start fresh |
| Review code line-by-line | Review research and plans |
| Have AI "roleplay" personas | Use subagents for context offload |

---

## 9. Verification Checklist

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

## 10. References

### FIC Methodology
- **Inspired by**: Dexter Horthy's "Research → Plan → Implement" pattern
- **Source**: "Advanced Context Engineering for Agents"
- **Link**: https://www.youtube.com/watch?v=IS_y40zY-hc
- **Adaptation**: "Compound" phase added for knowledge preservation

### S2S Framework
- **Source**: "Agile is Dead, Long Live S2S"
- **Link**: https://www.zerotopete.com/p/agile-is-dead-long-live-s2s

---

*Version: 1.1*
*Added: Section 0 (Context Engineering), Research output format, Subagent purpose, Common Mistakes, References*
