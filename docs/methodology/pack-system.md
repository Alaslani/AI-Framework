# PACK System

**P**roject **A**rtifacts for **C**ontext **K**nowledge

## Overview

The PACK system is the knowledge layer of AI-Framework. While FIC defines *how* to work and S2S defines *why* progress matters, PACK defines *what* to preserve.

## The Five Documents

### MASTER_REFERENCE — The Brain

Permanent project knowledge that compounds over time.

**Contains:**
- Project overview and architecture
- Key decisions with rationale
- Learnings (numbered, categorized)
- External integrations
- Quick reference (commands, IDs, URLs)

**Update when:** You learn something that changes future behavior.

**Anti-pattern:** Using it as a changelog. Only add *learnings*, not activities.

---

### ROADMAP — The Plan

Project direction and current priorities.

**Contains:**
- Priority queue (numbered tasks)
- Timeline and milestones
- Status indicators (progress bars)
- Deferred items with reasons

**Update when:** Priorities shift or milestones complete.

**Anti-pattern:** Updating daily. Weekly or at milestones is enough.

---

### TRANSFER_PACK — The Memory

Session-to-session handoff document.

**Contains:**
- What was completed this session
- What's blocked and why
- What to do next session
- Any files created or modified

**Update when:** Every session end, without exception.

**Anti-pattern:** Copy-pasting chat logs. Synthesize what matters for the *next* session.

---

### NEXT_SESSION_PROMPT — The Ignition

The executable kickoff for the next session.

**Contains:**
- A pre-paste snapshot (checks to confirm before starting)
- The exact first message to paste, with read order and context recap
- The one open question that must be resolved before any work
- Critical guardrails carried into the session
- Anti-patterns to avoid this session

**Update when:** Every session end, alongside the Transfer Pack.

**Anti-pattern:** Duplicating the Transfer Pack. The Transfer Pack *describes* state; this *executes* — it's the guardrailed first move, not the history.

---

### PROMPT_TEMPLATES — The Language

Standardized commands for consistent AI interaction.

**Contains:**
- Command reference table
- Template structures for each workflow
- Project-specific requirements
- Verification checklists

**Update when:** You discover better prompt patterns.

**Anti-pattern:** Over-engineering simple prompts. Match complexity to task.

---

## How They Work Together

```
Session Start:
  1. Paste NEXT_SESSION_PROMPT (the kickoff from last session)
  2. Read MASTER_REFERENCE (understand project)
  3. Read TRANSFER_PACK (understand where we left off)
  4. Check ROADMAP (understand priorities)
  5. Use PROMPT_TEMPLATES (communicate effectively)

Session End:
  1. Update TRANSFER_PACK (handoff)
  2. Write NEXT_SESSION_PROMPT (the ignition for next time)
  3. Add learnings to MASTER_REFERENCE (compound)
  4. Update ROADMAP if priorities changed
```

The Transfer Pack and Next Session Prompt are a pair, written together at session end: one records *state*, the other turns it into the *first move*.

## Naming Convention

```
PROJECT_MASTER_REFERENCE_v{major}.{minor}.md
PROJECT_TRANSFER_PACK_v{major}.{minor}.md
PROJECT_NEXT_SESSION_PROMPT_v{major}.{minor}.md
PROJECT_Development_Roadmap_v{major}.{minor}.md
PROJECT_Prompt_Templates.md
```

Version when content significantly changes. Transfer Pack and Next Session Prompt version with sessions.

---

*PACK + FIC + S2S = Complete AI-assisted development methodology.*
