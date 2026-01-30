# AI-Framework Competitive Analysis

> Research Date: 2026-01-30
> Phase 1/5: Competitive Analysis

---

## Executive Summary

The AI development methodology space has matured significantly, with several frameworks reaching 30-70k stars. **Three dominant patterns emerge**: (1) skills/agent-based composability (BMAD, Superpowers, CrewAI), (2) phased workflows with validation gates (ContextKit, AIDD, Disciplined-AI), and (3) memory/context management layers (Mem0, Awesome-Context-Engineering). Most frameworks focus on code generation but lack **cross-project knowledge transfer**, **decision logging for learning**, and **business-context integration**. The gap we can fill is a **lightweight, language-agnostic methodology** that combines the best patterns without requiring specific tooling dependencies.

---

## Repos Analyzed

### 1. BMAD-METHOD
- **URL**: https://github.com/bmad-code-org/BMAD-METHOD
- **Stars**: 32.9k
- **Structure**:
  ```
  docs/           # Documentation
  src/            # Source code
  tools/          # Utility scripts
  website/        # Documentation site
  ```
- **Key Concepts**:
  - 21 specialized agents across 4 phases (Analysis → Planning → Implementation → QA)
  - Slash-command workflow (`/quick-spec`, `/dev-story`, `/code-review`)
  - Scale-adaptive intelligence (adjusts from bug fixes to enterprise systems)
  - Treats AI as "expert collaborator" not autonomous executor
- **Gaps**:
  - Heavy Node.js dependency (requires npm install)
  - Complex setup for simple projects
  - No cross-project learning mechanism

---

### 2. Superpowers
- **URL**: https://github.com/obra/superpowers
- **Stars**: 40k
- **Structure**:
  ```
  skills/         # Composable skill modules
  agents/         # Agent definitions
  commands/       # CLI commands
  hooks/          # Integration hooks
  lib/            # Core libraries
  docs/           # Documentation
  ```
- **Key Concepts**:
  - Skills as composable building blocks (test-driven-development, systematic-debugging, etc.)
  - Git worktree isolation for parallel work
  - Two-stage review with subagent verification
  - Emphasis on 2-5 minute tasks for predictability
  - RED-GREEN-REFACTOR as anchoring discipline
- **Gaps**:
  - Highly opinionated about git workflow
  - No memory/context persistence between sessions
  - Assumes Claude Code environment

---

### 3. Prompt-Engineering-Guide
- **URL**: https://github.com/dair-ai/Prompt-Engineering-Guide
- **Stars**: 69.7k (highest in analysis)
- **Structure**:
  ```
  guides/         # Educational content
  notebooks/      # Jupyter examples
  lecture/        # Video materials
  pages/          # Next.js web app
  ar-pages/       # Arabic translations (13 languages)
  ```
- **Key Concepts**:
  - Comprehensive educational format (web + notebooks + video)
  - Technique library: zero-shot, few-shot, chain-of-thought, RAG
  - Model-specific guidance (GPT-4, Claude, Llama, Gemini)
  - Risk awareness: adversarial prompting, factuality, bias
- **Gaps**:
  - Focus on individual prompts, not workflows
  - No project structure recommendations
  - Educational resource, not actionable framework

---

### 4. Mem0
- **URL**: https://github.com/mem0ai/mem0
- **Stars**: 46.3k
- **Structure**:
  ```
  mem0/           # Core Python package
  mem0-ts/        # TypeScript SDK
  server/         # Backend service
  cookbooks/      # Usage examples
  evaluation/     # Benchmarking
  ```
- **Key Concepts**:
  - Multi-level memory: User, Session, Agent state
  - 26% accuracy improvement over OpenAI Memory (LOCOMO benchmark)
  - 91% faster responses than full-context approaches
  - Cross-platform SDKs with managed service option
- **Gaps**:
  - Infrastructure product, not methodology
  - Requires hosted service for best experience
  - No development workflow guidance

---

### 5. CrewAI
- **URL**: https://github.com/crewAIInc/crewAI
- **Stars**: 43.4k
- **Structure**:
  ```
  src/my_project/
  ├── crew.py           # Crew definition
  ├── config/
  │   ├── agents.yaml   # Agent roles/goals
  │   └── tasks.yaml    # Task assignments
  └── tools/            # Custom tools
  ```
- **Key Concepts**:
  - Agents defined via YAML (role, goal, backstory)
  - Crews for collaborative teams, Flows for event-driven workflows
  - Process types: Sequential vs Hierarchical (auto-assigns manager)
  - Logical operators (`or_`, `and_`) for complex triggering
- **Gaps**:
  - Python-specific implementation
  - Focuses on agent execution, not methodology
  - No guidance on when to use AI vs not

---

### 6. ContextKit
- **URL**: https://github.com/FlineDev/ContextKit
- **Stars**: 142 (smaller but methodologically strong)
- **Structure**:
  ```
  Context/Features/
  ├── 001-FeatureName/
  │   ├── Spec.md       # Business requirements
  │   ├── Tech.md       # Technical approach
  │   └── Steps.md      # Implementation tasks (S001-S999)
  ├── 002-QuickTask.md  # Single-file quick tasks
  ├── Context.md        # Project configuration
  └── CLAUDE.md         # Session context
  ```
- **Key Concepts**:
  - 4-phase methodology: Business Case → Tech Architecture → Tasks → Development
  - Quick workflow for smaller tasks (interactive validation)
  - Backlog as AI-managed database (never manually edited)
  - Inbox for user-editable quick capture
- **Gaps**:
  - Swift/SwiftUI focused in examples
  - Limited to Claude Code environment
  - No cross-project learning

---

### 7. AI Development Patterns
- **URL**: https://github.com/PaulDuvall/ai-development-patterns
- **Stars**: 306
- **Structure**:
  ```
  Patterns organized by lifecycle phase:
  ├── Foundation/       # Team readiness, security
  ├── Development/      # Daily workflows
  ├── Operations/       # CI/CD, production
  └── Experimental/     # Advanced patterns
  ```
- **Key Concepts**:
  - 22 documented patterns with maturity levels
  - Dependency mapping between patterns
  - Implementation timeline guidance (Weeks 1-6+)
  - Task sizing: 4-8 hour standard, 1-2 hour atomic
  - Anti-patterns documented for each pattern
- **Gaps**:
  - Catalog format, not executable framework
  - No tooling or automation
  - Enterprise-focused, may be overkill for small projects

---

### 8. AIDD Framework
- **URL**: https://github.com/paralleldrive/aidd
- **Stars**: 187
- **Structure**:
  ```
  ai/               # Agent orchestration (rules, commands, workflows)
  plan/             # Story maps, user journeys
  docs/             # User testing guides
  vision.md         # Project source-of-truth
  activity-log.md   # Change tracking
  ```
- **Key Concepts**:
  - Vision document as single source of truth
  - 7 primary commands: `/discover`, `/task`, `/execute`, `/review`, `/log`, `/commit`, `/user-test`
  - SudoLang for structured workflow definitions
  - Story maps for user journey documentation
- **Gaps**:
  - Proprietary SudoLang requirement
  - Steep learning curve
  - Limited community adoption

---

### 9. Disciplined AI Software Development
- **URL**: https://github.com/Varietyz/Disciplined-AI-Software-Development
- **Stars**: 391
- **Structure**:
  ```
  persona/              # AI behavioral constraints
  prompt_formats/       # Template formats
  pag_templates/        # Project templates
  example_project_structures/
  METHODOLOGY.md        # Core methodology
  PAG-AGENT-ORCHESTRATION.md
  ```
- **Key Concepts**:
  - **150-line file size limit** (hard constraint)
  - Phase 0: Benchmarking infrastructure first
  - Validation gates at phase boundaries
  - Addresses context dilution and architectural drift
  - Persona framework for behavioral enforcement
- **Gaps**:
  - Very prescriptive (may feel restrictive)
  - 150-line limit controversial for real projects
  - No flexibility for different project types

---

### 10. Awesome-Context-Engineering
- **URL**: https://github.com/Meirtz/Awesome-Context-Engineering
- **Stars**: 2.9k
- **Structure**:
  ```
  README.md with sections:
  ├── Definition & Theory
  ├── Components & Techniques
  ├── Implementation (RAG, memory, tools)
  ├── Evaluation & Benchmarks
  └── Future Directions
  ```
- **Key Concepts**:
  - Context engineering = "complete information payload at inference time"
  - Memory taxonomy: Neural, Production (Mem0), Graph-based
  - RAG variants and multi-hop reasoning
  - Benchmark inventory (HotpotQA, LOCOMO, MemoryAgentBench)
- **Gaps**:
  - Survey/reading list, not actionable
  - Academic focus over practical application
  - No project structure recommendations

---

## Patterns to Adopt

| Pattern | Source | Why Adopt |
|---------|--------|-----------|
| **Phased Development** | ContextKit, BMAD | Clear progression prevents scope creep |
| **Spec → Tech → Steps** | ContextKit | Separates "what" from "how" from "do" |
| **Skills as Composable Units** | Superpowers | Reusable building blocks for workflows |
| **Slash Commands** | BMAD, AIDD | Familiar UX, quick access to workflows |
| **Validation Gates** | Disciplined-AI | Quality checkpoints prevent drift |
| **Activity Logging** | AIDD | Track decisions for learning |
| **Multi-level Memory** | Mem0 | User + Session + Project context |
| **Quick Task Path** | ContextKit | Not everything needs full planning |
| **Anti-patterns Documentation** | AI-Dev-Patterns | Learn from mistakes, not just successes |
| **Maturity Levels** | AI-Dev-Patterns | Progressive adoption path |

---

## Gaps We Fill

| Gap | Our Solution |
|-----|--------------|
| **No cross-project learning** | Knowledge base that persists patterns across projects |
| **Heavy tooling dependencies** | Pure markdown files, zero dependencies |
| **Language-specific** | Language-agnostic methodology (works with any stack) |
| **No decision logging** | ADR-style decision log for AI interactions |
| **Business context ignored** | Business-first templates (vision → requirements → tech) |
| **All-or-nothing adoption** | Modular adoption (pick what you need) |
| **Enterprise-only or toy projects** | Scale-adaptive (solo dev to team) |
| **No "when to use AI" guidance** | Decision framework for AI vs manual |
| **Session-only context** | Project-level CLAUDE.md + personal overrides |
| **No learning loop** | Retrospective template for continuous improvement |

---

## Recommended Structure (Updated)

Based on analysis, our AI-Framework should follow this structure:

```
ai-framework/
├── README.md                    # Quick start (< 5 min to value)
├── PHILOSOPHY.md                # Core principles
│
├── methodology/
│   ├── 00-when-to-use-ai.md    # Decision framework
│   ├── 01-research-phase.md    # Understand before acting
│   ├── 02-planning-phase.md    # Spec → Tech → Steps
│   ├── 03-implementation.md    # Execute with validation
│   ├── 04-review-phase.md      # Quality gates
│   └── 05-retrospective.md     # Learning loop
│
├── patterns/
│   ├── beginner/               # Start here
│   ├── intermediate/           # Once comfortable
│   └── advanced/               # Power users
│
├── templates/
│   ├── CLAUDE.md.template      # Project instructions
│   ├── feature-spec.md         # Feature requirements
│   ├── tech-design.md          # Technical approach
│   ├── task-list.md            # Implementation checklist
│   ├── adr.md                  # Decision record
│   └── retro.md                # Retrospective
│
├── skills/                      # Composable skills (like Superpowers)
│   ├── research.md
│   ├── planning.md
│   ├── debugging.md
│   ├── code-review.md
│   └── testing.md
│
├── examples/
│   ├── solo-project/           # Individual developer
│   ├── startup/                # Small team
│   └── enterprise/             # Larger organization
│
└── memory/
    ├── project-patterns.md     # Cross-project learnings
    ├── decision-log.md         # ADR-style log
    └── anti-patterns.md        # What to avoid
```

### Key Differentiators

1. **Zero dependencies** - Pure markdown, works everywhere
2. **5-minute onboarding** - Copy templates, start working
3. **Modular adoption** - Use only what you need
4. **Scale-adaptive** - Same principles, different depth
5. **Cross-project memory** - Learn from past projects
6. **Decision transparency** - Log why, not just what
7. **When NOT to use AI** - Guidance on manual vs assisted

---

## Next Steps

- [ ] Phase 2: Define core methodology principles
- [ ] Phase 3: Create template files
- [ ] Phase 4: Write beginner patterns
- [ ] Phase 5: Build example projects

---

## Sources

- [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - 32.9k stars
- [Superpowers](https://github.com/obra/superpowers) - 40k stars
- [Prompt-Engineering-Guide](https://github.com/dair-ai/Prompt-Engineering-Guide) - 69.7k stars
- [Mem0](https://github.com/mem0ai/mem0) - 46.3k stars
- [CrewAI](https://github.com/crewAIInc/crewAI) - 43.4k stars
- [ContextKit](https://github.com/FlineDev/ContextKit) - 142 stars
- [AI Development Patterns](https://github.com/PaulDuvall/ai-development-patterns) - 306 stars
- [AIDD Framework](https://github.com/paralleldrive/aidd) - 187 stars
- [Disciplined AI Software Development](https://github.com/Varietyz/Disciplined-AI-Software-Development) - 391 stars
- [Awesome-Context-Engineering](https://github.com/Meirtz/Awesome-Context-Engineering) - 2.9k stars
