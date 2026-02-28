# Claude Code Integration

Claude Code is a terminal-based AI coding agent. This guide covers patterns that maximize its effectiveness with AI-Framework.

> Based on practices from the Anthropic Claude Code team (Boris Cherny, Feb 2026; Thariq, Feb 2026).

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

### Tool Count Matters

The Claude Code team maintains ~20 tools total. Every additional command or agent is one more option the model must evaluate before acting — cognitive load, not capability.

**Guideline**: If your project has more than ~20 total commands + agents, you likely have overlap. Audit for:

- Commands that run the same underlying checks
- Agents that cover the same domain
- Skills that duplicate CLAUDE.md content

Fewer, well-designed tools outperform many overlapping ones.

---

## Progressive Disclosure

**Problem**: CLAUDE.md is loaded every conversation. If it contains detailed patterns, examples, and reference tables, that's a token tax on every session — even when the content is irrelevant to the task at hand.

**Solution**: Keep CLAUDE.md lean (~200-400 lines). Move detailed content to skill files. Replace verbose sections with 1-2 line pointers.

```markdown
# In CLAUDE.md (always loaded)
## Database → See .claude/skills/database/ for query patterns, migration templates

# In .claude/skills/database/SKILL.md (loaded on demand)
[Full query patterns, RLS examples, migration templates — 400+ lines]
```

### Progressive Disclosure Hierarchy

```
CLAUDE.md → skills → docs → external docs
```

Each layer references the next. The model reads recursively to build exactly the context it needs — no more, no less.

### What Goes Where

| Keep in CLAUDE.md | Move to skill files |
|-------------------|---------------------|
| Tech stack and versions | Code patterns and examples |
| Git conventions | Reference tables |
| Verification commands | Testing templates and fixtures |
| File structure overview | Design system details |
| Key architectural decisions | Compliance and security checklists |

### Rule of Thumb

If a section is only relevant to 1 in 5 sessions, it belongs in a skill file, not CLAUDE.md.

This is the same pattern Claude Code uses internally — skill files reference other files, the model discovers context by following pointers recursively.

---

## Periodic Tool Audit

Run every 20-30 sessions. Audit your commands, agents, skills, and CLAUDE.md token count.

### Overlap Signals

| Signal | Action |
|--------|--------|
| Two commands run the same verification checks | Merge (one command, add flags) |
| Multiple agents cover the same domain | Consolidate into one |
| Skill duplicates a CLAUDE.md section | Extract from CLAUDE.md, keep skill |
| Command unused for a month | Remove — the model can improvise |
| Commands and agents don't reference each other | Connect or consolidate |

### Orthogonal vs Overlapping

This is the critical distinction. Only merge things that genuinely overlap.

| Relationship | Example | Action |
|-------------|---------|--------|
| Overlapping | `/verify` and `/deploy` run identical checks | Merge: `/deploy --dry-run` |
| Subset | `/bundle-analyze` is part of `/perf-audit` | Absorb into parent |
| Orthogonal | Accessibility audit vs visual polish | Keep separate |

Merging orthogonal concerns into one bloated command is **worse** than two focused ones. Accessibility (WCAG compliance) and visual polish are different skills — combining them dilutes both.

### Constraining Language Audit

Review your CLAUDE.md and skill files for language that limits model adaptation.

| Pattern | Risk | Fix |
|---------|------|-----|
| "Always follow X workflow" | Forces overhead on trivial tasks | "For non-trivial features, prefer X" |
| "ALWAYS do Y" | Over-applied to irrelevant contexts | Scope: "When doing Z, always Y" |
| System reminders every N turns | Model sticks rigidly to lists | Remove if model handles natively |
| Extended thinking keywords | Cargo cult | Remove or make optional |

The Claude Code team learned this when TodoWrite reminders every 5 turns made Claude stick rigidly to the todo list instead of adapting. They replaced TodoWrite with Tasks — and removed the system reminders. When models get smarter, old tools and rigid instructions can constrain rather than help. Revisit your assumptions.

---

## Slot Machine Pattern (Autonomous Mode)

Checkpoint → autonomous work (15-30 min) → binary accept/revert. Don't wrestle with broken output — revert and re-prompt.

Source: Anthropic's RL Engineering and Data Science teams.

| Good for | Never for |
|----------|-----------|
| Tests and test generation | Business logic |
| Code cleanup and formatting | Security-critical code |
| Refactoring (well-tested code) | Database migrations |
| Multi-file repetitive changes | Financial calculations |

The key insight: starting over with a better prompt beats wrestling with fixes. If the output isn't right, revert the checkpoint and try again with clearer instructions.

---

## Session End Protocol

Before closing a session, run through these steps:

1. **Summarize** — What was accomplished this session
2. **Capture learnings** — What surprised you, what broke, what worked
3. **Update CLAUDE.md** — If the model made a mistake you corrected, add it so the same mistake doesn't recur
4. **Identify blockers** — What's preventing the next step
5. **Generate Transfer Pack** — Handoff for the next session

**Key**: The CLAUDE.md self-improvement loop. Mistakes discovered → add to CLAUDE.md → next session avoids them. This creates a continuous improvement cycle where every session makes the project smarter.

Source: Anthropic's Data Infrastructure team.

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

### 4. Parallel Exploration

When both paths seem viable and you need data to decide, not opinions:

```
"Implement approach A in one branch and approach B in another.
Compare: performance, code complexity, and maintainability."
```

Use git worktrees to run both approaches simultaneously. Let concrete results pick the winner.

**When to use which**:
- First pass feels hacky → Elegant Solution
- About to ship → Grill
- Need confidence → Prove It Works
- Architectural fork → Parallel Exploration
- Peripheral task, don't want to supervise → Slot Machine

---

## /statusline

Always-visible context usage and git status:

```
/statusline
```

Shows: context % used, current branch, model. Helps you catch the 60% threshold before it's too late.

---

## Parallel Development with Git Worktrees

One git worktree = one working directory = one Claude Code session. No branch switching, no stashing, no conflicts.

### Setup

```bash
# Create worktrees from your repo root
git worktree add ../project-feat origin/main
git worktree add ../project-fix origin/main
```

### The Pattern

```
Terminal 1 (main)          Terminal 2 (feat)          Terminal 3 (fix)
┌──────────────┐          ┌──────────────┐          ┌──────────────┐
│ Claude Code  │          │ Claude Code  │          │ Claude Code  │
│ main branch  │          │ feat/dark-   │          │ fix/rtl-     │
│ tests + QA   │          │ mode         │          │ header       │
└──────────────┘          └──────────────┘          └──────────────┘
       │                         │                         │
       └─────────────────────────┼─────────────────────────┘
                                 │
                           Same .git repo
                           Shared history
```

### Rules

| Rule | Why |
|------|-----|
| One Claude Code instance per worktree | Isolated context = better output |
| Only parallelize independent work | Dependent tasks create merge conflicts |
| Merge back to main frequently | Avoid drift between worktrees |
| Clean up finished worktrees | `git worktree remove ../project-feat` |

### When to Parallelize

**Good candidates**: Feature A + Bug fix B (different files), Backend + Frontend (different domains), Tests + Docs (no code overlap).

**Don't parallelize**: Two features touching same files, tasks with data dependencies, database migrations.

### Cleanup

```bash
git worktree list              # List active worktrees
git worktree remove ../feat    # Remove finished worktree
git worktree prune             # Prune stale references
```

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
