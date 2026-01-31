# Cross-Project Learning

Knowledge that compounds across all your work.

## The Gap We Fill

Most AI workflows lose knowledge:
- Chat history expires
- Context resets
- Learnings stay in one project
- Patterns aren't transferred

## Transferable Knowledge

### Universal Patterns

Approaches that work regardless of project:

- **Research before implement** — Always pays off
- **40% context rule** — Keeps AI reasoning sharp
- **Signal over status** — Capture why, not just what
- **Verify at gates** — Prevents cascading errors

### Technology Patterns

Learnings about specific tools:

```markdown
## Supabase Learnings
1. RLS policies need SECURITY DEFINER for cross-table
2. Foreign keys should reference auth.users
3. Migrations must be idempotent

## Next.js Learnings
1. Server components can't use hooks
2. Use 'use client' sparingly
3. Middleware runs on edge
```

### Process Patterns

How you work effectively:

- Best prompt structures
- Effective planning templates
- Review checklists that catch bugs

## Structure for Reuse

### Personal Knowledge Base

Maintain across projects:

```
~/knowledge/
├── patterns/
│   ├── supabase.md
│   ├── nextjs.md
│   └── testing.md
├── anti-patterns/
│   └── common-mistakes.md
├── templates/
│   └── [reusable templates]
└── decisions/
    └── architecture-choices.md
```

### Tagging System

Categorize learnings for retrieval:

- `#database` — Database-related
- `#security` — Security patterns
- `#performance` — Optimization
- `#process` — Workflow improvements

## Compounding Over Time

### Weekly Review
- What patterns emerged this week?
- What mistakes did I repeat?
- What should I document?

### Project Retrospective
- What learnings transfer?
- What templates should I update?
- What would I do differently?

### Quarterly Cleanup
- Archive outdated learnings
- Update templates with improvements
- Share useful patterns

## Starting Fresh

When starting a new project:

1. Copy relevant templates
2. Review applicable patterns
3. Check anti-patterns to avoid
4. Set up MASTER_REFERENCE with known decisions

---

*Next: [Notion Integration](../integrations/notion.md)*
