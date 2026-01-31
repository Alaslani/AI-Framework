# PACK System

**P**roject **A**rtifacts for **C**ontext **K**nowledge

## Overview

The PACK system is the knowledge layer of AI-Framework. While FIC defines *how* to work and S2S defines *why* progress matters, PACK defines *what* to preserve.

## The Four Documents

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
  1. Read MASTER_REFERENCE (understand project)
  2. Read TRANSFER_PACK (understand where we left off)
  3. Check ROADMAP (understand priorities)
  4. Use PROMPT_TEMPLATES (communicate effectively)

Session End:
  1. Update TRANSFER_PACK (handoff)
  2. Add learnings to MASTER_REFERENCE (compound)
  3. Update ROADMAP if priorities changed
```

## Naming Convention

```
PROJECT_MASTER_REFERENCE_v{major}.{minor}.md
PROJECT_TRANSFER_PACK_v{major}.{minor}.md
PROJECT_Development_Roadmap_v{major}.{minor}.md
PROJECT_Prompt_Templates.md
```

Version when content significantly changes. Transfer Pack versions with sessions.

---

*PACK + FIC + S2S = Complete AI-assisted development methodology.*
