# When to Use AI

> Decision framework for AI-assisted vs manual development.

## Quick Decision Tree

```
Is it exploration/research?
├── Yes → AI excels here
└── No ↓

Is it a quick fix (<50 lines)?
├── Yes → Either works
└── No ↓

Do you understand the codebase?
├── No → AI for research first
└── Yes ↓

Is it security-critical?
├── Yes → AI drafts, human verifies everything
└── No → AI can handle most of it
```

---

## AI Strengths

| Task | Why AI Excels |
|------|---------------|
| Code search | Faster than grep, understands context |
| Boilerplate | Repetitive code without typos |
| Refactoring | Consistent transformations |
| Documentation | Generates drafts from code |
| Test writing | Covers edge cases you'd miss |
| Debugging | Pattern recognition at scale |
| Learning | Explains unfamiliar code |

## AI Weaknesses

| Task | Why AI Struggles |
|------|------------------|
| Novel architecture | No training data for your context |
| Business decisions | Doesn't understand your business |
| Security validation | May miss vulnerabilities it created |
| Performance tuning | Needs actual profiling data |
| Long context | Quality degrades as context grows |

---

## Role Distribution

| Phase | AI Role | Human Role |
|-------|---------|------------|
| Exploration | Navigator | Question-asker |
| Planning | Collaborator | Decision-maker |
| Implementation | Executor | Reviewer |
| Review | Checker | Approver |
| Debugging | Investigator | Validator |

---

## The 40% Rule

Keep context under 40% capacity.

**Why?**
- Better reasoning with less noise
- More accurate code generation
- Faster responses

**How?**
- One phase per context (complex tasks)
- Clear between unrelated work
- Write progress to files

---

## Thinking Depth Keywords

| Keyword | When to Use |
|---------|-------------|
| "think" | Basic reasoning |
| "think hard" | Complex logic, architecture |
| "think harder" | Multi-system changes |
| "ultrathink" | Security, payments, critical |

---

## Task Sizing

| Type | Scope | Workflow |
|------|-------|----------|
| Quick fix | <50 lines, 1 file | Direct |
| Standard | 1-3 files, clear scope | Brief plan |
| Multi-phase | Many files | Full workflow |
| Epic | Multiple sessions | Break down, track |

---

## Red Flags: Stop and Reset

- AI repeating same approach
- Output looks right but unexplainable
- Context window full
- 3+ failed attempts
- Hallucinating functions/files

**Recovery:**
1. Stop generating
2. Reset context
3. Document what you know
4. Different approach

---

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| "AI will figure it out" | Vague → wrong output | Be specific |
| "Let it run" | Errors compound | Validate each step |
| "Trust but don't verify" | Bugs ship | Always test |
| "More context is better" | Quality degrades | Stay lean |

---

## Summary

```
Use AI for:    Exploration, boilerplate, refactoring, docs, tests
Use Human for: Decisions, validation, security, business logic
Use Both for:  Planning, implementation, debugging
```
