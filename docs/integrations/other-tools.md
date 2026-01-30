# Using AI-Framework with Other Tools

AI-Framework is tool-agnostic. Here's how to adapt it for different AI coding assistants.

---

## Cursor

Cursor uses `.cursorrules` for project context.

### Mapping

| AI-Framework | Cursor Equivalent |
|--------------|-------------------|
| MASTER_REFERENCE.md | `.cursorrules` |
| PROMPT_TEMPLATES.md | Chat commands |
| Transfer Pack | Chat history export |
| CLAUDE.md | `.cursorrules` (same file) |

### Setup

1. Copy MASTER_REFERENCE content to `.cursorrules`
2. Add to `.cursorrules`:
```
## Workflow
Follow FIC methodology:
1. /research - Understand before acting
2. /plan - Define phases before implementing
3. /implement - One phase at a time
4. /compound - Capture learnings

## Rules
- Keep context under 40%
- Include file paths with line numbers in research
- Verify after each phase (type check, lint, build, test)
```

3. Use `/research`, `/plan`, `/implement` as chat commands
4. Manually write PROGRESS.md between sessions

### Tips
- Cursor's Composer mode = our "Implement" phase
- Use `@file` to add specific files to context
- "Long context" mode for larger research phases

---

## Windsurf

Windsurf uses `.windsurfrules` for project context.

### Mapping

| AI-Framework | Windsurf Equivalent |
|--------------|---------------------|
| MASTER_REFERENCE.md | `.windsurfrules` |
| Research phase | Cascade "Understand" mode |
| Plan phase | Cascade "Plan" mode |
| Implement phase | Cascade "Execute" mode |

### Setup

1. Copy MASTER_REFERENCE content to `.windsurfrules`
2. Use Cascade modes that map to FIC:
   - **Understand** → Research phase (read files, trace flow)
   - **Plan** → Planning phase (define approach)
   - **Execute** → Implementation phase (write code)

3. After each session, update Transfer Pack manually

### Tips
- Windsurf's Flows = multi-step workflows
- Use @-mentions for file context
- "Superprompt" feature for complex instructions

---

## GitHub Copilot

Copilot doesn't have persistent context, so adapt the approach:

### Mapping

| AI-Framework | Copilot Equivalent |
|--------------|---------------------|
| MASTER_REFERENCE.md | Keep open in tab, reference in comments |
| Research phase | Use Copilot Chat with `@workspace` |
| Plan phase | Write plan in comments, ask Copilot to review |
| Implement phase | Inline completions + Chat |

### Setup

1. Keep MASTER_REFERENCE.md open in a tab
2. Reference it in comments:
```typescript
// See MASTER_REFERENCE.md Section 3 for auth patterns
// Following Learning #5: Use optimistic updates
```

3. Use Copilot Chat for research:
```
@workspace Where is authentication handled?
@workspace What's the data flow for orders?
```

4. For planning, write comments first:
```typescript
// Plan:
// 1. Add new database column
// 2. Update API endpoint
// 3. Modify frontend form
// Copilot: Review this plan for issues
```

### Tips
- Smaller, focused prompts work better than long context
- Use `/explain` for research, `/fix` for debugging
- `#file:path` to reference specific files

---

## Aider

Aider supports repo-level context.

### Mapping

| AI-Framework | Aider Equivalent |
|--------------|------------------|
| MASTER_REFERENCE.md | `--read` flag or `/read` command |
| Research phase | `/ask` mode |
| Plan phase | `/architect` mode |
| Implement phase | Default `/code` mode |

### Setup

```bash
# Start with master reference in context
aider --read MASTER_REFERENCE.md

# Or add during session
/read MASTER_REFERENCE.md
```

### Workflow

```bash
# Research phase
/ask How does the authentication system work?
/ask What files handle order processing?

# Plan phase
/architect I need to add user notifications

# Implement phase (default)
Add email notification when order ships
```

### Tips
- Use `/tokens` to monitor context usage (40% rule)
- `/clear` between unrelated tasks
- `/commit` with conventional commit messages
- `--auto-commits` for automatic git integration

---

## Cline (VS Code)

Cline is a VS Code extension for Claude.

### Mapping

| AI-Framework | Cline Equivalent |
|--------------|------------------|
| MASTER_REFERENCE.md | Add to context with @ |
| Research phase | "Understand" tool usage |
| Plan phase | Ask for plan before implementation |
| Implement phase | "Edit" tool usage |

### Setup

1. Create `.clinerules` with methodology:
```markdown
## Workflow
1. Always read relevant files before editing
2. Propose plan before making changes
3. Verify after each change

## Context Management
- Keep conversation focused
- Start new chat for unrelated tasks
- Reference MASTER_REFERENCE.md for project context
```

2. Use the `@` symbol to add files to context

### Tips
- Cline's "auto-approve" mode = faster implementation
- Use task descriptions for complex multi-step work
- Export chat history for Transfer Packs

---

## General Principles

Regardless of which tool you use, these principles apply:

### 40% Rule
All AI tools have context limits. Keep usage under 40% for best results.

### FIC Workflow
The Research → Plan → Implement → Compound workflow works everywhere:
1. **Research**: Understand before acting
2. **Plan**: Define approach before coding
3. **Implement**: One phase at a time with verification
4. **Compound**: Capture learnings for next time

### Transfer Packs
Session handoffs are universal. Always document:
- What was completed
- Current state
- Next steps
- New learnings

### Spec-First
The description of what to build matters more than the code. Write specs, let AI generate code.

### Verification
After every implementation phase:
```bash
1. Type check
2. Lint
3. Build
4. Test
```

---

## Tool Comparison

| Feature | Claude Code | Cursor | Windsurf | Copilot | Aider | Cline |
|---------|-------------|--------|----------|---------|-------|-------|
| Persistent context | ✅ CLAUDE.md | ✅ .cursorrules | ✅ .windsurfrules | ❌ | ✅ --read | ✅ .clinerules |
| Multi-file edits | ✅ | ✅ | ✅ | ⚠️ Limited | ✅ | ✅ |
| Git integration | ✅ | ✅ | ✅ | ⚠️ | ✅ | ✅ |
| Context visibility | ✅ | ✅ | ✅ | ❌ | ✅ /tokens | ⚠️ |
| Terminal access | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ |

---

*Adapt AI-Framework to your preferred tool. The methodology is what matters, not the specific implementation.*
