# AI Memory System

Optimize how AI assistants remember your project.

## The Challenge

AI memory is:
- Limited in slots
- Consumed on every conversation
- Reset between sessions (mostly)
- Shared across all your projects

## Memory Slot Allocation

Optimal: **5-7 lines** of memory instructions.

### Recommended Structure

```
1. Project files: [DOC1] (read first), [DOC2]. Always latest version.
2. Workflow: [Method]. [Key process]. Max [N] phases.
3. Outputs: When creating [X], also update [Y].
4. Working style: [Verification], [confidence level], [communication].
5. Interaction: [Question approach], [depth preference].
```

### Example

```
1. Project files: MASTER_REF (read first), PROMPTS, TRANSFER_PACK. Always latest.
2. Workflow: FIC method. Notion phased (Research→Plan→Execute). Max 5 phases.
3. Outputs: Create Transfer_Pack + update MASTER_REF with learnings.
4. Working style: Verify before act, state confidence, brief chat.
5. Interaction: ONE question until 95% confident, docs rigorous.
```

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| ❌ Too vague | "Be helpful" | Already default—remove |
| ❌ Too detailed | 20+ lines | Put in external doc |
| ❌ Redundant | Same rule twice | Combine |
| ❌ One-time tasks | "Fix bug X" | Use task tracker |
| ❌ Volatile info | "Current sprint" | Changes too often |

## CLAUDE.md Convention

Project-level AI instructions in your repository:

```
project/
├── CLAUDE.md           # Checked into git
├── CLAUDE.local.md     # Gitignored (personal prefs)
└── features/
    └── auth/
        └── CLAUDE.md   # Feature-specific context
```

### CLAUDE.md Content

```markdown
# Project: [Name]

## Context
[2-3 sentences about the project]

## Tech Stack
[Key technologies]

## Conventions
[Coding standards, patterns used]

## Current Focus
[What's being worked on]
```

## Memory vs. Documents

| Put in Memory | Put in Documents |
|---------------|------------------|
| Workflow preferences | Detailed procedures |
| Communication style | Architecture details |
| File references | API documentation |
| Output expectations | Historical decisions |

---

*Next: [Cross-Project Learning](cross-project-learning.md)*
