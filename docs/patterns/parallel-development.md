# Parallel Development with Git Worktrees

Run multiple AI agents simultaneously on independent tasks.

---

## Why Worktrees?

One git worktree = one working directory = one Claude Code session. No branch switching, no stashing, no conflicts. Three terminals, three features, one repo.

## Setup

```bash
# Create worktrees from your repo root
git worktree add ../project-feat origin/main
git worktree add ../project-fix origin/main

# Naming convention: short aliases
# za = main, zb = feature, zc = fix
```

### Shell Aliases (Optional)

```bash
# ~/.zshrc or ~/.bashrc
alias za='cd /path/to/project-main'
alias zb='cd /path/to/project-feat'
alias zc='cd /path/to/project-fix'
```

One keystroke to switch between worktrees.

## The Pattern

```
Terminal 1 (za)          Terminal 2 (zb)          Terminal 3 (zc)
┌──────────────┐         ┌──────────────┐         ┌──────────────┐
│ Claude Code  │         │ Claude Code  │         │ Claude Code  │
│ main branch  │         │ feat/dark-   │         │ fix/rtl-     │
│ tests + QA   │         │ mode         │         │ header       │
└──────────────┘         └──────────────┘         └──────────────┘
       │                        │                        │
       └────────────────────────┼────────────────────────┘
                                │
                          Same .git repo
                          Shared history
```

## Rules

| Rule | Why |
|------|-----|
| One Claude Code instance per worktree | Isolated context = better output |
| Only parallelize independent work | Dependent tasks create merge conflicts |
| Merge back to main frequently | Avoid drift between worktrees |
| Clean up finished worktrees | `git worktree remove ../project-feat` |

## When to Parallelize

✅ **Good candidates:**
- Feature A + Bug fix B (different files)
- Backend API + Frontend component (different domains)
- Tests + Documentation (no code overlap)
- Research in one session + Implementation in another

❌ **Don't parallelize:**
- Two features touching the same files
- Tasks with data dependencies (B needs A's output)
- Database migrations (only one at a time)

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Two agents editing same file | Merge conflicts, lost work | Serialize or split file |
| Forgetting to merge back | Worktrees drift apart | Merge after each feature completes |
| Too many worktrees (5+) | Mental overhead | Stick to 3, clean up after |
| Using worktrees for context overflow | Treats symptoms, not cause | Fix context management instead |

## Cleanup

```bash
# List active worktrees
git worktree list

# Remove a finished worktree
git worktree remove ../project-feat

# Prune stale references
git worktree prune
```

---

*Back to: [Context Warning Signs](context-warning-signs.md) · [Session Handoff](session-handoff.md)*
