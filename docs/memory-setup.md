# AI Memory Setup

Configure your AI to remember your project across sessions.

---

## The Rule

**Memory should be 4-7 lines.** Not a document. A cheat sheet.

---

## Template (Copy & Fill)
```
[Name] is [role] of [Project] ([one-line description]). Stack: [tech stack]. [Key architecture note].

Follows FIC workflow (Find→Implement→Compound). Uses Transfer Packs for session handoff. 90%+ Mode: verify before act, one question until 95% confident.

Project files: MASTER_REF (read first), PROMPTS, Transfer_Pack, Roadmap. Always latest version.

Brief chat, rigorous docs. Ask before token-heavy tools (MCP, web search). Create Transfer Pack + update MASTER_REF at session end, ask [Name] to upload.
```

---

## Line-by-Line Breakdown

| Line | Content | Purpose |
|------|---------|---------|
| 1 | Role + Project + Stack | Who you are, what you're building |
| 2 | Workflow + Handoff + Principle | How you work |
| 3 | Project files + Priority | What to read first |
| 4 | Preferences + End-of-session | Rules to follow |

---

## What Each Part Means

### Line 1: Identity
```
[Name] is [role] of [Project] ([one-line description]). Stack: [tech stack]. [Key architecture note].
```
- **Name**: Your name
- **Role**: CTO, founder, lead, solo dev
- **Project**: Project name
- **Description**: What it does in 5 words
- **Stack**: Main technologies
- **Architecture note**: Portals, services, key structure

### Line 2: Methodology
```
Follows FIC workflow (Find→Implement→Compound). Uses Transfer Packs for session handoff. 90%+ Mode: verify before act, one question until 95% confident.
```
- **FIC**: The workflow you follow
- **Transfer Packs**: How you hand off between sessions
- **90%+ Mode**: Your verification principle

### Line 3: Files
```
Project files: MASTER_REF (read first), PROMPTS, Transfer_Pack, Roadmap. Always latest version.
```
- **File list**: What files exist
- **Priority**: Which to read first
- **Version note**: Always use latest

### Line 4: Rules
```
Brief chat, rigorous docs. Ask before token-heavy tools (MCP, web search). Create Transfer Pack + update MASTER_REF at session end, ask [Name] to upload.
```
- **Communication style**: Brief vs verbose
- **Tool usage**: When to ask permission
- **End-of-session**: What to do before closing

---

## Platform Setup

### Claude.ai
Settings → Memory → Edit → Paste your 4 lines

### Claude Projects
Add to Project Instructions (not Knowledge)

### Cursor / Windsurf
`.cursorrules` or `.windsurfrules` in project root

### API / Custom
System prompt prefix (keep under 200 tokens)

---

## What NOT to Include

| Exclude | Why |
|---------|-----|
| Current tasks | Stale in hours |
| Session history | No signal |
| Full learnings | Belongs in MASTER_REF |
| Tool configs | Belongs in project files |
| Detailed instructions | Too long |

---

## Memory vs Project Files

| Memory (4 lines) | Project Files (unlimited) |
|------------------|---------------------------|
| Who + How | What + Why |
| Points to files | Contains details |
| AI settings | Repo |
| Rarely changes | Updated often |

**Memory says "read MASTER_REF." MASTER_REF has the details.**

---

## Maintenance

| When | Action |
|------|--------|
| Project pivot | Update line 1 |
| Workflow change | Update line 2 |
| New files | Update line 3 |
| New rules | Update line 4 |
| Never | Add session details |

---

## Final Step: Ask Your AI to Remember

After filling in your template, tell your AI agent:
```
Add this to your memory:

[Paste your 4 lines here]
```

**For updates**, tell your AI:
```
Update your memory with this:

[Paste updated lines here]
```

The AI will confirm the memory has been saved. Verify by starting a new session and checking if the AI knows your project context.

---

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

---

## Memory Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Too vague | "Be helpful" | Already default — remove |
| Too detailed | 20+ lines | Put in external doc |
| Redundant | Same rule twice | Combine |
| One-time tasks | "Fix bug X" | Use task tracker |
| Volatile info | "Current sprint" | Changes too often |

---

*If your memory is longer than 4-7 lines, it's too long.*
