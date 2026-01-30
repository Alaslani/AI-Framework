# Notion Integration Best Practices

> Extracted from internal documentation, generalized for universal use.
> Research Date: 2026-01-30 | Phase 2/5

---

## Source

Patterns synthesized from 18+ development sessions using AI-assisted development. Based on S2S (Spec to Signal) and FIC (Find → Implement → Compound) methodologies.

---

## Workflow Patterns

### 1. FIC Methodology (Find → Implement → Compound)

The core workflow for AI-assisted development:

```
┌─────────────────────────────────────────────┐
│  1. RESEARCH (~200 lines)                   │
│     ├── Understand: files, data flow        │
│     ├── Output: research/[feature].md       │
│     └── CHECKPOINT: "Understanding OK?"     │
│                    ↓                        │
│  2. PLAN (~200 lines)                       │
│     ├── Define: phases, files, verification │
│     ├── Output: plans/[feature].md          │
│     └── CHECKPOINT: "Approach OK?"          │
│                    ↓                        │
│  3. IMPLEMENT (context < 40%)               │
│     ├── Execute: one phase at a time        │
│     ├── Verify: after each phase            │
│     └── Compact: when context > 40%         │
│                    ↓                        │
│  4. COMPOUND                                │
│     ├── Capture learnings                   │
│     └── Update knowledge base               │
└─────────────────────────────────────────────┘
```

### 2. The Pyramid Principle

```
Bad Research    = 1000s of bad lines of code
Bad Plan        = 100s of bad lines of code
Bad Code        = 1 bad line of code
```

**Key Insight:** Focus human effort on HIGHEST LEVERAGE parts: Research and Planning.

### 3. S2S (Spec to Signal) Methodology

> "AI collapsed the cost of implementation. What used to take weeks now takes hours. When execution became cheap, the constraint moved. Clarity is now the bottleneck."

Three phases:
1. **Thinking (Quiet):** Articulate intent carefully. Write specs as testable hypotheses.
2. **Building (Fast):** AI generates code; humans judge quality.
3. **Listening (Continuous):** Monitor telemetry as evidence, not metrics.

### 4. Context Management Rules

1. **Keep context under 40%** - Better reasoning, less noise
2. **One phase per context** for complex tasks
3. **Compact intentionally** - Write progress to plan.md before context resets
4. **Start fresh** when context gets polluted
5. **Clear between tasks** - Reset context between unrelated work

### 5. Thinking Depth Keywords

| Keyword | When to Use |
|---------|-------------|
| "think" | Basic reasoning |
| "think hard" | Complex logic, architecture |
| "think harder" | Multi-system changes |
| "ultrathink" | Security, payments, critical systems |

### 6. Task Size Classification

| Type | Duration | Workflow |
|------|----------|----------|
| Quick fix | <50 lines, single file | Direct implementation |
| Standard task | 1 session | Plan briefly, implement |
| Multi-phase | 2+ sessions | Full FIC workflow |
| Epic | Multiple weeks | Break into phases, track in database |

---

## Database Organization

### Task Tracking Schema

A complete project tracking system with five interconnected databases:

#### Tasks Database
| Property | Type | Values |
|----------|------|--------|
| Name | Title | - |
| Area | Select | Feature areas (e.g., Frontend, Backend, Infra) |
| Priority | Select | P0 (Critical), P1 (High), P2 (Normal) |
| Type | Select | Task, Bug Fix, Feature, Research, Improvement |
| Status | Status | Not started, In progress, Done, Blocked |
| Effort | Select | XS, S, M, L, XL |
| Target Date | Date | - |
| Completed | Date | Auto-set when done |
| Signal | Text | Key learning from this task |
| Phase | Relation | Links to Phases database |

#### Bugs Database
| Property | Type | Values |
|----------|------|--------|
| Name | Title | - |
| Area | Select | Same as Tasks |
| Severity | Select | Critical, High, Medium, Low |
| Status | Status | Confirmed, Verified, Blocked, Fixed, Won't Fix |
| Environment | Select | Production, Staging, Local |
| Priority | Select | P0, P1, P2 |
| Reported Date | Date | Auto-set on creation |
| Fixed Date | Date | Auto-set when fixed |
| Steps to Reproduce | Text | - |
| Expected/Actual | Text | - |
| Root Cause | Text | Filled when fixed |
| Related Task | Relation | Links to Tasks |

#### Phases Database
Tracks project milestones and their associated tasks.

#### Team Database
| Property | Type | Values |
|----------|------|--------|
| Name | Title | - |
| Role | Select | Engineering roles |
| Availability | Select | Full-time, Part-time, Contractor, Advisor |
| Status | Select | Active, On Leave, Offboarded |
| Skills | Multi-select | Technology stack |
| Email | Email | - |

#### Releases Database
Tracks deployments with linked completed tasks.

### Database Relationships
```
Phases ←──── Tasks ←──── Bugs
   │            │
   └── Team ────┘
         │
    Releases
```

---

## Phased Task Management

### When to Track

**Rule:** Only multi-phase work gets tracked. Quick fixes don't need tracker entries.

### Multi-Phase Feature Workflow

1. **Phase 1:** Create task → Get task ID → Do work → Update signal
2. **Phases 2-N:** Do work → Update signal with learnings
3. **Final Phase:** Do work → Complete task with final signal

### Bug During Work

1. Create bug with related task reference
2. Decide: Fix now or defer
3. When fixed: Document fix and root cause

### Signal-Based Updates

Instead of status-only updates, capture the **signal** - what you learned:

```
❌ Bad:  status = "In progress"
✅ Good: status = "In progress", signal = "RLS bypass requires SECURITY DEFINER"
```

---

## Integration Guidelines

### Task Tracker + Code Agent Integration

When using AI coding assistants with task tracking:

1. **CREATE** task in tracker (source of truth)
2. **START** coding session with task reference
3. **EXECUTE** work (agent manages subtasks internally)
4. **SYNC** back to tracker on completion with signal

### Session Naming Convention

```
<area>-<feature>

Examples:
- auth-oauth-flow
- api-rate-limiting
- ui-dark-mode
- infra-caching
```

### Transfer Pack System

For passing context between sessions (keep under 80 lines):

```markdown
# TRANSFER PACK vX.X

**Session**: [ID] | **Date**: [DATE] | **Status**: [BRIEF]

## Session Summary
**Completed**: [Task 1] ✅, [Task 2] ✅
**Key Commits**: `abc123` - feat(scope): description

## Current State
[2-3 sentences - what's working, what's not]

## Task Queue
| # | Task | Priority | Status |
|---|------|----------|--------|
| 1 | [Next task] | P0 | ⏳ Next |
| 2 | [Following task] | P1 | Planned |

## Next Steps
1. [Immediate action with thinking level]
2. [Following action]

## Files Changed (This Session)
- [path/to/file.tsx]

## New Learnings
| # | Learning |
|---|----------|
| 1 | [Concise learning to add to knowledge base] |
```

---

## Living Documentation Patterns

### CLAUDE.md Convention

Project-level AI instructions that evolve with the codebase:

```
project/
├── CLAUDE.md              # Top-level vision (checked into git)
├── CLAUDE.local.md        # Personal prefs (gitignored)
└── features/
    └── auth/
        ├── requirements.md
        └── implementation.md
```

**Key Principles:**
- Document current state only (git handles history)
- Subdirectory files provide focused context
- Migrate repeated instructions into documentation
- Keep lean - reference detailed docs instead of duplicating

### Memory Optimization (for Claude.ai)

**Rule:** Every memory edit consumes tokens in EVERY conversation. Keep essential only.

**Slot Allocation (5-7 lines optimal):**
```
1. Project files: [DOC1] (read first), [DOC2]. Always use latest.
2. Workflow: [Method]. [Key process]. Max [N] phases.
3. Outputs: When asked for [X], create [Y] AND update [Z].
4. Working style: [Verification], [confidence], [communication].
5. Interaction: [Question approach], [depth preference].
```

**Anti-patterns to avoid:**
- ❌ Too vague: "Be helpful" (already default)
- ❌ Too detailed: Put in external document instead
- ❌ Redundant: Combine related rules
- ❌ One-time tasks: Use task tracker
- ❌ Context that changes: Update overhead not worth it

---

## Key Principles

### 1. Clarity Over Speed

> "The work is closing the distance between specification and evidence."

AI makes implementation fast; knowing what to build is now the bottleneck.

### 2. Signal Over Ceremony

Replace standups and status reports with:
- Telemetry and usage data
- Error rates and friction metrics
- Evidence-based interpretation

> "A good week is one where assumptions were invalidated early, cheaply, and without drama."

### 3. The Blindfold Analogy

Product development as orientation:
1. Take a step
2. If wrong, step back
3. Adjust
4. Repeat

**Key:** Not perfect planning, but relentless correction.

### 4. Orchestrator Mindset

The developer's role shifts from implementation to orchestration:
1. Articulate intent clearly
2. Manage parallel workstreams
3. Delegate effectively
4. Maintain living knowledge

### 5. Verification Protocol

After every implementation:
1. Type check
2. Lint
3. Build
4. Test on actual environment

### 6. Lessons Learned System

Capture hard-won knowledge:

| Category | What to Document |
|----------|------------------|
| Gotchas | Silent failures, constraint violations |
| Patterns | Successful approaches to repeat |
| Anti-patterns | Approaches that failed and why |
| Decisions | Why certain approaches were chosen |

**Format for each lesson:**
- Context: Why this came up
- Wrong approach: What didn't work
- Correct approach: What does work
- Why: The underlying reason

---

## Quick Reference

### Workflow Selection

| Complexity | Workflow |
|------------|----------|
| Single file, <50 lines | Direct fix |
| 1-3 files, clear scope | Plan briefly → Implement |
| Multi-file, new feature | Research → Plan → Implement → Compound |
| Multi-session, complex | Full FIC with tracker integration |

### Progress Metrics (S2S Aligned)

Measure by:
- ✅ Assumptions tested
- ✅ Uncertainty removed
- ✅ Evidence gathered

Not by:
- ❌ Lines of code written
- ❌ Tickets closed
- ❌ Hours worked

### Agent Selection Guide

```
Is it API/Database? ---------> Backend specialist
Is it UI/Components? --------> Frontend specialist
Is it deployment/infra? -----> DevOps specialist
Is it tests? ----------------> QA specialist
Is it research/docs? --------> Research specialist
Not sure? -------------------> Start with /research
```

---

## Adoption Path

1. **Week 1:** Adopt FIC workflow (Research → Plan → Implement)
2. **Week 2:** Add context management (40% rule, compaction)
3. **Week 3:** Implement task tracking with signals
4. **Week 4:** Establish lessons learned system
5. **Ongoing:** Refine based on team learnings

---

*Synthesized from FIC methodology, S2S framework, and Zero to Pete AI-native development patterns.*
