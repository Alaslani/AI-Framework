# References

## Methodology Sources

### FIC (Find → Implement → Compound)
- **Inspired by**: Dexter Horthy's "Research → Plan → Implement" pattern
- **Source video**: "Advanced Context Engineering for Agents"
- **Creator**: Dexter Horthy, Founder of Human Layer
- **Link**: https://www.youtube.com/watch?v=IS_y40zY-hc
- **Original insight**: Spec-first development, intentional context management, keeping context utilization under 40%
- **Adaptation**:
  - "Research" renamed to "Find" (same purpose: understand system, locate files)
  - "Compound" phase added for knowledge preservation and cross-session learning
- **Adapted by**: AI-Framework methodology

### S2S (Spec to Signal)
- **Source**: "Agile is Dead, Long Live S2S"
- **Author**: Pete (Zero to Pete)
- **Link**: https://www.zerotopete.com/p/agile-is-dead-long-live-s2s
- **Key insight**: Progress = reduced uncertainty, not throughput
- **Flow**: Spec (intent, assumptions) → Build (AI-collapsed) → Deploy → Signal (evidence) → Learn
- **Why it matters**: When execution cost collapses, clarity becomes the bottleneck. The work shifts from building to learning.

### Claude Code Best Practices
- **Source**: "10 Tips for Claude Code" thread
- **Author**: Boris Cherny (@bcherny), Anthropic Claude Code team
- **Date**: February 1, 2026
- **Platform**: X (Twitter)
- **Key patterns**: Parallel worktrees, plan-then-review, CLAUDE.md investment, prompt escalation, /rewind, subagent spawning, permission hooks
- **Adapted into**: `docs/patterns/parallel-development.md`, `docs/integrations/claude-code.md`

## Real-World Implementation

### SahmX
- **What**: Saudi Arabian real estate tokenization platform
- **Scale**: 3 production portals, 60+ day sprint to launch
- **Repo**: Private (methodology extracted into this framework)
- **Validation**: 340+ learnings captured, 50+ sessions managed

## External Resources

### Documentation Patterns
- [Bulletproof React](https://github.com/alan2207/bulletproof-react) — Project structure inspiration
- [Anthropic Prompt Engineering](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering) — Official Claude docs
- [Cursor Directory](https://cursor.directory/) — Community prompt patterns

### AI-Assisted Development
- [Human Layer](https://humanlayer.dev/) — Agent orchestration patterns
- [Context7](https://context7.com/) — Library documentation for AI
- [Claude Code Plugin Marketplace](https://claudemarketplaces.com/) — Community directory for Claude Code plugins and extensions

## Academic & Research

- Context window management in LLMs (emerging field)
- Multi-agent coordination patterns
- Knowledge preservation in AI workflows

---

*Framework synthesized from FIC methodology, S2S framework, and production experience.*
