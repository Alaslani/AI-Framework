# Claude Code Integration

Claude Code is a terminal-based AI coding agent. This guide covers patterns that maximize its effectiveness with AI-Framework.

> Based on practices from the Anthropic Claude Code team (Boris Cherny, Feb 2026).

---

## Plan Mode

Toggle with `Shift+Tab`. Use for **both** planning and verification.

### Plan-Then-Review Pattern

```
Step 1: Ask Claude to plan the implementation (plan mode)
Step 2: Review the plan yourself
Step 3: Switch to code mode for execution
Step 4: Toggle back to plan mode — ask Claude to review its own work as a staff engineer
```

This catches issues that implementation mode misses because the model shifts from "get it done" to "find what's wrong."

---

## /rewind — Recover Without Starting Fresh

When stuck, use `/rewind` (or press `ESC` twice) to roll back conversation turns.

**Key benefit**: Claude automatically summarizes the rewound portion, so learnings survive the rollback.

```
Before /rewind:
  Turn 1: Research ✓
  Turn 2: Plan ✓
  Turn 3: Implement → broke things
  Turn 4: Fix attempt → made it worse
  Turn 5: Another fix → spiraling

After /rewind to Turn 2:
  Turn 1: Research ✓
  Turn 2: Plan ✓
  [Summary: "Attempted X and Y, both failed because Z"]
  Turn 3: New approach with Z in mind
```

Use `/rewind` **before** starting a fresh context. See [Context Warning Signs](../patterns/context-warning-signs.md).

---

## CLAUDE.md Investment

`CLAUDE.md` is your project's persistent memory across sessions.

### The Correction Loop

Every time Claude makes a mistake you've seen before:

```
You: "Update CLAUDE.md so you don't make that mistake again"
```

Over time, `CLAUDE.md` accumulates your project's tribal knowledge. The mistake rate drops with each correction.

### Notes Directory Pattern

For projects with many context files:

```
CLAUDE.md                          # Core rules (keep under 5k tokens)
  → points to: .claude/learnings/  # Detailed notes
    ├── gotchas.md                 # Known issues and workarounds
    ├── architecture.md            # System design decisions
    └── testing.md                 # Test patterns and fixtures
```

`CLAUDE.md` stays lean. Detailed knowledge lives in referenced files.

---

## Skills and Commands

If you do something more than once a day, it should be a skill or custom command.

```
.claude/
├── commands/        # Custom slash commands
│   ├── test.md      # /test — run project-specific test suite
│   ├── deploy.md    # /deploy — deployment checklist
│   └── review.md    # /review — code review prompt
└── skills/          # Reusable skills (commit to git)
    ├── rtl-check/   # Check RTL layout compliance
    └── api-test/    # Generate API test from route file
```

Skills are reusable across projects. Commands are project-specific.

---

## Subagent Patterns

Append "use subagents" to throw more compute at a problem.

### When to Use Subagents

```
Main agent: "Implement the payment flow. Use subagents for:
- Database schema research
- API route implementation
- Test writing"
```

Each subagent gets its own context window — keeps the main agent focused.

### Permission Hooks

Route safe operations to auto-approve instead of manual confirmation:

```
# .claude/hooks/auto-approve.sh
# Auto-approve: read-only operations, test runs, lint
# Manual approve: file writes, git commits, deployments
```

See: https://code.claude.com/docs/en/hooks

---

## Prompt Escalation Patterns

Three patterns for pushing Claude beyond its first attempt:

### 1. The Elegant Solution

After a working but messy first pass:

```
"Knowing everything you know now, scrap this and implement
the elegant solution."
```

Forces a clean rewrite using all the context gathered during the first attempt.

### 2. The Grill

Before merging:

```
"Grill me on these changes. Don't make a PR until I pass your test."
```

Claude becomes your code reviewer. It will find edge cases, missing tests, and logic errors.

### 3. Prove It Works

After implementation:

```
"Prove to me this works. Diff the behavior between main and this branch."
```

Claude runs both versions and compares output — concrete proof, not just assertions.

---

## /statusline

Always-visible context usage and git status:

```
/statusline
```

Shows: context % used, current branch, model. Helps you catch the 60% threshold before it's too late.

---

## Context Management

| Signal | Action |
|--------|--------|
| Context > 40% | Consider wrapping up current task |
| Context > 60% | `/rewind` or start fresh |
| Agent loops 3+ times | `/rewind` to last good state |
| "Let me try another approach" | Stop — see [Warning Signs](../patterns/context-warning-signs.md) |

---

## Tool Comparison

| Feature | Claude Code | Cursor | Windsurf | Aider |
|---------|-------------|--------|----------|-------|
| Persistent context | CLAUDE.md | .cursorrules | .windsurfrules | --read |
| Plan mode | Shift+Tab | ❌ | Cascade modes | /architect |
| Subagents | ✅ Native | ❌ | ❌ | ❌ |
| Conversation rewind | /rewind | ❌ | ❌ | /clear |
| Custom commands | .claude/commands/ | ❌ | ❌ | ❌ |
| Git worktrees | ✅ (recommended) | ⚠️ Per-window | ⚠️ | ✅ |
| Permission hooks | ✅ | Auto-approve | Auto-approve | --auto-commits |
| Terminal-native | ✅ | ❌ (IDE) | ❌ (IDE) | ✅ |

---

*Back to: [Other Tools](other-tools.md) · [Prompt Templates](../../templates/PROMPT_TEMPLATES.md)*
