# Templates

Copy this entire folder to your project root to get started.

## Contents

| File | Purpose |
|------|---------|
| `MASTER_REFERENCE.md` | Project knowledge base — fill once, update always |
| `TRANSFER_PACK.md` | Session handoff — create at end of each session |
| `NEXT_SESSION_PROMPT.md` | Executable kickoff — the first message to paste next session |
| `ROADMAP.md` | Priority queue and timeline |
| `PROMPT_TEMPLATES.md` | AI prompt patterns for your workflow |
| `commands/security-scan.md` | Drop-in `/security-scan` Claude Code command (copy to `.claude/commands/`) |

## Quick Start

```bash
cp -r templates/ /path/to/your/project/
```

Then customize each file for your project.

## Claude Code Commands

The `commands/` subfolder holds ready-to-drop slash-command templates. Copy a
file into your project's `.claude/commands/` and adapt the `[bracketed]` parts:

```bash
cp templates/commands/security-scan.md /path/to/your/project/.claude/commands/
```

`security-scan.md` pairs with the
[Semgrep SAST + /security-scan](../docs/methodology/semgrep-sast.md) guide.
