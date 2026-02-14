# Project Instructions Template for Claude AI

> Copy the section below into your Claude AI Project Instructions.
> Then upload your filled PACK documents as Project Knowledge files.

---

## How to Use

### Step 1: Create a Claude AI Project

Go to claude.ai → Projects → New Project

### Step 2: Set Project Instructions

Copy everything inside the [PASTE INTO PROJECT INSTRUCTIONS] section below
and paste it into your Project's custom instructions.

Fill in the [BRACKETED] placeholders with your project details.

### Step 3: Upload Project Knowledge

Upload these filled-in files as Project Knowledge:

- Your `MASTER_REFERENCE.md`
- Your `PROMPT_TEMPLATES.md`
- Your `TRANSFER_PACK.md` (replace after each session)
- Your `ROADMAP.md`

### Step 4: Start Working

Open a new chat in your Project. Claude will automatically read all
Project Knowledge files and follow your instructions.

---

## [PASTE INTO PROJECT INSTRUCTIONS]

```
You are working on [PROJECT NAME] — [one-line description].

## Reading Order (every new chat)
1. MASTER_REFERENCE — project context, architecture, learnings
2. TRANSFER_PACK — where we left off, next steps
3. PROMPT_TEMPLATES — available commands (/research, /plan, /implement)
4. ROADMAP — current priorities

## Workflow
Follow FIC (Find → Implement → Compound):
- /research before complex work — understand files, data flow, dependencies
- /plan for multi-file changes — max 3-5 phases, define verification per phase
- /implement one phase at a time — verify after each phase
- /compound to capture learnings at session end

## Session Rules
- 90%+ Mode: verify before acting, ask questions until 95% confident
- Brief chat responses, rigorous documentation
- Create Transfer Pack at session end — ask me to upload it
- Update MASTER_REFERENCE with new learnings
- Ask before using token-heavy tools (web search, web fetch)
- If conversation gets long (15+ messages), suggest starting a new chat

## Verification (after every implementation phase)
1. Type check
2. Lint
3. Build
4. Test

## Project-Specific Context
- Stack: [your tech stack]
- Key conventions: [RTL, i18n, test patterns, etc.]
- Production URLs: [your URLs]
- [Add anything the AI needs to know every session]
```

---

## Updating Between Sessions

After each session:

1. Ask Claude to generate a new Transfer Pack
2. Download or copy it
3. Go to Project → Knowledge → replace old TRANSFER_PACK.md with new one
4. If MASTER_REFERENCE was updated, replace that too

---

## Tips

- **Keep Project Instructions under 500 tokens** — put details in Knowledge files
- **One Project per product** — don't mix projects in one Claude Project
- **Transfer Pack is the bridge** — it's what gives Claude continuity between chats
- **Memory is separate** — Claude Memory persists across ALL projects,
  Project Knowledge is scoped to one project. See `docs/memory-setup.md`.
