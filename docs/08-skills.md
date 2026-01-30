# Skills

> Composable capabilities for AI-assisted development.

## Overview

Skills are focused capabilities that combine into workflows.

```
Research → Planning → Implementation → Testing → Review
    ↑                      ↑              ↑
    └── Debugging ─────────┴──────────────┘
```

---

## Skill: Research

**Purpose:** Understanding codebases before acting

### Commands
```
"What files handle [X]?"
"Trace the flow from [A] to [B]"
"What does [function] do?"
"Find all usages of [X]"
```

### Output
~200 lines documenting:
- Structure
- Data flow
- Patterns
- Key files

---

## Skill: Planning

**Purpose:** Designing approach before implementing

### Commands
```
"Plan implementation of [feature]"
"What are options for [decision]?"
"Break this into phases"
"What's the minimal approach?"
```

### Output
Plan with:
- Success criteria
- Technical approach
- Phased steps
- Verification points

---

## Skill: Debugging

**Purpose:** Finding and fixing issues systematically

### Commands
```
"What does this error mean?"
"What could cause [symptom]?"
"Trace source of [error]"
"What changed to cause this?"
```

### Workflow
1. Reproduce
2. Isolate
3. Investigate
4. Hypothesize
5. Fix
6. Verify

---

## Skill: Testing

**Purpose:** Writing and running tests

### Commands
```
"Write tests for [function]"
"What edge cases to test?"
"Generate test cases for [scenario]"
"Why is this test failing?"
```

### Test Types
| Type | Purpose | Mocking |
|------|---------|---------|
| Unit | Individual functions | Dependencies |
| Integration | Components together | External only |
| E2E | Full flows | None/external |

---

## Skill: Code Review

**Purpose:** Validating code quality

### Commands
```
"Review for bugs"
"What edge cases aren't handled?"
"Review for security issues"
"Any performance concerns?"
```

### Checklist
- Correctness
- Tests
- Security
- Performance
- Style

---

## Combining Skills

| Task | Skills |
|------|--------|
| Quick fix | Maybe debugging |
| Bug fix | Debug + Test |
| Feature | Research + Plan + Test + Review |
| Refactor | Research + Plan + Test |

---

## Skill Development

Create custom skills:

```markdown
# Skill: [Name]

## When to Use
[Conditions]

## Commands
[Effective prompts]

## Workflow
[Step-by-step]

## Output
[Expected result]
```

---

*Skills combine into workflows. Master each, then compose them.*
