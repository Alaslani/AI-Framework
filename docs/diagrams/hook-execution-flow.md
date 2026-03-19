# Hook Execution Flow

How Claude Code hooks interact with tool execution during a session.

```mermaid
flowchart TD
    REQ([Tool Request]) --> PRE{PreToolUse}
    PRE -->|Exit 0| TOOL[Tool Executes]
    PRE -->|Non-zero| BLOCKED([Blocked])
    TOOL --> POST[PostToolUse]
    POST --> MORE{More work?}
    MORE -->|Yes| REQ
    MORE -->|No| STOP{Stop Hook}
    STOP -->|Exit 0| DONE([Done])
    STOP -->|Non-zero| FIX([Fix Issues])
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
