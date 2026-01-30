# Scaling

> From solo developer to enterprise team.

## Solo Developer

### Setup
```
project/
├── CLAUDE.md              # Project context
└── .notes/                # Working docs (gitignored)
    ├── current-task.md
    └── learnings.md
```

### Workflow
- Keep CLAUDE.md updated
- Track current task in .notes/
- Capture learnings as you go

### Minimum Viable Process
1. CLAUDE.md for context
2. Verify every change
3. Optional: quick notes

---

## Small Team (3-10)

### Setup
```
project/
├── CLAUDE.md              # Shared context
├── .claude/               # Team settings
│   ├── patterns.md
│   └── gotchas.md
├── docs/
│   ├── architecture.md
│   ├── decisions/
│   └── specs/
└── .notes/                # Gitignored
    └── [developer]/
```

### Workflow
- Shared CLAUDE.md for consistency
- Team patterns in .claude/
- Decision records for major choices
- Feature specs for coordination

### Coordination
- PR descriptions for context
- Decision log for rationale
- Weekly pattern sharing

---

## Enterprise

### Setup
```
org-standards/             # Org-wide repo
├── CLAUDE.md              # Org guidelines
├── patterns/
├── templates/
└── compliance/

project/                   # Per-project
├── CLAUDE.md              # Project-specific
├── .claude/
└── docs/
```

### Layered Context
```
Organization → Team → Project
   ↓           ↓        ↓
Guidelines  Patterns  Specific
```

### Governance
| Aspect | Requirement |
|--------|-------------|
| AI usage | Policy defined |
| Review | Human required |
| Security | Checklist verified |
| Audit | Trail maintained |

### Training
1. AI tool basics
2. Security requirements
3. Team workflows
4. Certification

---

## Knowledge Sharing

### Solo
- Personal notes
- Capture learnings

### Team
- Shared patterns file
- Gotchas document
- Decision records
- Retrospective sharing

### Enterprise
- Central pattern library
- Cross-team reviews
- Training programs
- Metrics tracking

---

## Adoption Path

### Phase 1: Pilot
- 1-2 developers/teams
- Basic CLAUDE.md
- Gather feedback

### Phase 2: Standards
- Create shared patterns
- Define policies
- Build training

### Phase 3: Rollout
- All teams adopt
- Establish governance
- Track metrics

### Phase 4: Optimize
- Regular reviews
- Tool evaluation
- Process refinement

---

## Common Patterns

| Scale | Pattern |
|-------|---------|
| Solo | Current task tracking |
| Team | Shared patterns, decision log |
| Enterprise | Layered context, governance |

---

## Anti-Patterns

| Scale | Anti-Pattern | Fix |
|-------|--------------|-----|
| Solo | No process | Minimum: CLAUDE.md |
| Team | Everyone different | Shared standards |
| Enterprise | Heavy governance | Balance usability |

---

*Start small. Add process as needed. Scale with the team.*
