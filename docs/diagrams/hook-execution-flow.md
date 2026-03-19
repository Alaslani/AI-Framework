# Hook Execution Flow

How Claude Code hooks interact with tool execution during a session.

```mermaid
flowchart LR
    REQ([Claude wants\nto use a tool]) --> PRE{PreToolUse\nHook}

    PRE -->|Exit 0\nAllowed| TOOL[Tool Executes\nWrite, Edit, Bash, etc.]
    PRE -->|Exit non-zero\nBlocked| BLOCKED([Blocked\nClaude sees reason\nand adjusts])

    TOOL --> POST[PostToolUse\nHook]
    POST --> ADVISORY([Advisory Output\nClaude sees warnings\nbut continues])

    ADVISORY --> MORE{More\nwork?}
    MORE -->|Yes| REQ
    MORE -->|No| STOP{Stop Hook\nSelf-Review}

    STOP -->|Exit 0\nClean| DONE([Done\nSession complete])
    STOP -->|Exit non-zero\nIssues found| FIX([Claude reviews\nfindings and fixes])
    FIX --> MORE

    style PRE fill:#e74c3c,color:#fff
    style TOOL fill:#27ae60,color:#fff
    style POST fill:#f5a623,color:#fff
    style STOP fill:#e74c3c,color:#fff
    style BLOCKED fill:#e74c3c,color:#fff
    style DONE fill:#27ae60,color:#fff
```

| Hook Type | When It Runs | Behavior | Use For |
|-----------|-------------|----------|---------|
| **PreToolUse** | Before tool execution | Blocks if exit non-zero | Protecting paths, enforcing rules |
| **PostToolUse** | After tool execution | Advisory only, never blocks | File size warnings, import checks |
| **Stop** | When Claude finishes | Can send Claude back to fix | Anti-rationalization, taste review |

**When to use:** Understanding how hooks fit into the Claude Code tool execution cycle, or debugging why a hook isn't triggering as expected.

*See: [Evals System](../methodology/evals-system.md)*
