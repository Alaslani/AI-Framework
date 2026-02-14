# Notion Integration

Using Notion for project tracking with AI workflows.

## Database Schema

A complete tracking system with five interconnected databases.

### Tasks Database

| Property | Type | Values |
|----------|------|--------|
| Name | Title | — |
| Area | Select | Frontend, Backend, Infra, etc. |
| Priority | Select | P0 (Critical), P1 (High), P2 (Normal) |
| Type | Select | Task, Bug Fix, Feature, Research |
| Status | Status | Not started, In progress, Done, Blocked |
| Effort | Select | XS, S, M, L, XL |
| Target Date | Date | — |
| Completed | Date | Auto-set when done |
| Signal | Text | Key learning from this task |

### Bugs Database

| Property | Type | Values |
|----------|------|--------|
| Name | Title | — |
| Severity | Select | Critical, High, Medium, Low |
| Status | Status | Confirmed, Fixed, Won't Fix |
| Environment | Select | Production, Staging, Local |
| Steps to Reproduce | Text | — |
| Root Cause | Text | Filled when fixed |
| Related Task | Relation | Links to Tasks |

### Phases Database

Milestones with linked tasks.

### Team Database

| Property | Type | Values |
|----------|------|--------|
| Name | Title | — |
| Role | Select | Engineering roles |
| Status | Select | Active, On Leave |
| Skills | Multi-select | Technologies |

### Releases Database

Deployments with linked completed tasks.

## Relationships

```
Phases ←──── Tasks ←──── Bugs
   │            │
   └── Team ────┘
         │
     Releases
```

## Signal-Based Tracking

### The Pattern

Every task completion includes a **signal**:

```
❌ Status: Done

✅ Status: Done
   Signal: "RLS policies need SECURITY DEFINER for service role access"
```

### Why Signals Matter

- Capture learning at the moment of insight
- Build searchable knowledge base
- Enable pattern recognition
- Help future debugging

## Task + AI Agent Sync

### Workflow

1. **CREATE** task in Notion (source of truth)
2. **REFERENCE** task ID when starting AI session
3. **UPDATE** status during work
4. **COMPLETE** with signal capturing learning

### Integration Pattern

```markdown
## Starting Session

"Working on Task #[ID]: [Name]
Current status: In Progress
Goal: [Task objective]"

## Ending Session

"Task #[ID] complete.
Signal: [Key learning]
Updating status to Done."
```

## Automation Ideas

- Auto-set "Completed" date when status → Done
- Daily summary of signals from completed tasks
- Weekly pattern analysis from signals

---

*Next: [Claude AI](claude-ai.md)*
