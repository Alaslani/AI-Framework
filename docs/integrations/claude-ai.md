# Claude AI Integration

How to use AI-Framework with Claude AI (chat interface).

> This covers claude.ai, the Claude app, and Claude Projects.
> For Claude Code (terminal agent), see [Claude Code Integration](claude-code.md).

---

## Quick Setup

1. Copy `PROJECT_INSTRUCTIONS.md` from this repo into your Claude Project
2. Upload your PACK documents as Project Knowledge
3. Start chatting — Claude knows your project context automatically

For detailed setup steps, see [PROJECT_INSTRUCTIONS.md](../../PROJECT_INSTRUCTIONS.md).

---

## Projects as Persistent Context

Claude Projects let you attach files that persist across every conversation
in that Project. This is where your PACK documents live.

### What to Upload as Project Knowledge

| File | Why |
|------|-----|
| MASTER_REFERENCE.md | AI reads project context every conversation |
| PROMPT_TEMPLATES.md | Commands available in every chat |
| TRANSFER_PACK.md | Replace after each session — continuity bridge |
| ROADMAP.md | Current priorities always visible |

### Project Knowledge vs File Uploads

| Project Knowledge | File Uploads (in chat) |
|-------------------|------------------------|
| Available in every chat | Available in one chat only |
| For PACK docs, style guides, API docs | For specific code files, screenshots, data |
| Replace when updated | Upload fresh each time |

**Rule**: If you need it every session → Project Knowledge.
If you need it once → file upload in chat.

---

## Memory Setup

Claude Memory persists across ALL conversations and ALL Projects.
Use it for identity and preferences, not project details.

### Template (4-7 lines max)

```
[Name] is [role] of [Project] ([one-line description]).
Stack: [tech]. [Key architecture note].

Follows FIC workflow (Find→Implement→Compound).
Uses Transfer Packs for session handoff.
90%+ Mode: verify before act, one question until 95% confident.

Project files: MASTER_REF (read first), PROMPTS, Transfer_Pack, Roadmap.
Always latest version.

Brief chat, rigorous docs.
Ask before token-heavy tools (web search, web fetch).
Create Transfer Pack + update MASTER_REF at session end.
```

### Memory vs Project Knowledge

| Memory (4-7 lines) | Project Knowledge (unlimited) |
|---------------------|-------------------------------|
| Who you are + how you work | What the project is + details |
| Persists across all Projects | Scoped to one Project |
| Rarely changes | Updated per session |
| Identity, preferences, workflow | Architecture, learnings, priorities |

**Memory says "read MASTER_REF." Project Knowledge has the actual file.**

### What NOT to Put in Memory

| Exclude | Why |
|---------|-----|
| Current tasks | Stale in hours |
| Session history | No signal value |
| Full learnings list | Belongs in MASTER_REF |
| Detailed instructions | Too long — use Project Instructions |

---

## Session Workflow

### Starting a Session

1. Open your Project in claude.ai
2. Claude automatically has your PACK docs in context
3. State your task using commands: `/research [name]` or `/plan [name]`
4. Claude reads MASTER_REF and TRANSFER_PACK automatically

### During a Session

- Use commands from PROMPT_TEMPLATES directly in chat
- Upload files if Claude needs to see specific code
- Use thinking keywords: "think hard" / "think harder" / "ultrathink"
- If conversation gets long (15+ messages), start a new chat in the same Project
- Ask Claude to create artifacts for docs you want to download

### Ending a Session

1. Ask Claude: "Create a Transfer Pack for this session"
2. Download the Transfer Pack (or copy it)
3. Replace the old TRANSFER_PACK.md in Project Knowledge
4. If new learnings emerged: ask Claude to update MASTER_REFERENCE too

---

## Context Management (No /rewind)

Claude AI doesn't have /rewind. Recovery is different from Claude Code:

| Signal | Action |
|--------|--------|
| Same error 3+ times | Stop → new chat in same Project |
| AI loops without progress | Stop → ask for PROGRESS.md → new chat |
| Conversation >15 messages | Consider starting fresh |
| "Let me try a different approach" | Red flag — save progress, new chat |
| Context feels polluted | New chat — Project Knowledge carries over automatically |

**Key advantage of Projects**: Starting a new chat costs nothing.
Your PACK docs are always there. No re-uploading, no context loss.

---

## Artifacts for Output

Claude AI can create artifacts (code, documents, diagrams).
Use them for outputs you need to save:

| Use Case | Artifact Type |
|----------|---------------|
| Research doc | Markdown artifact |
| Implementation plan | Markdown artifact |
| Transfer Pack | Markdown artifact |
| Code file | Code artifact |
| Architecture diagram | Mermaid artifact |

Ask Claude: "Create this as an artifact" when you need to download or save.

---

## What Claude AI Can't Do (Use Claude Code Instead)

| Need | Claude AI | Claude Code |
|------|-----------|-------------|
| Read your filesystem | Upload files | Direct access |
| Run commands (build, test) | No | Yes |
| Git operations | No | Yes |
| Spawn subagents | No | Yes |
| Edit files in-place | No | Yes |
| /rewind conversation | No | Yes |

**Workflow split**: Claude AI for research, planning, document creation,
and strategy. Claude Code for implementation and verification.

---

## Combining Claude AI + Claude Code

Many developers use both. Here's the split:

| Phase | Use | Why |
|-------|-----|-----|
| Research (/research) | Claude AI | Better for reading, analysis, planning |
| Planning (/plan) | Claude AI | Better for multi-step reasoning |
| Implementation (/implement) | Claude Code | Direct file access, can run tests |
| Verification | Claude Code | Can run type check, lint, build, test |
| Compound (/compound) | Claude AI | Better for synthesis, Transfer Packs |
| Notion / external tools | Claude AI | MCP tools, web search |

### Handoff Pattern

1. Research + Plan in Claude AI → create plan artifact
2. Copy plan to your repo or paste into Claude Code
3. Implement in Claude Code following the plan
4. Return to Claude AI for Compound + Transfer Pack

---

*Back to: [Claude Code Integration](claude-code.md) · [Other Tools](other-tools.md)*
