# Philosophy

> Core principles that guide AI-assisted development.

## The Fundamental Insight

**AI collapsed the cost of implementation.** What took weeks now takes hours. When execution became cheap, the constraint moved.

**Clarity is now the bottleneck.**

The work is no longer writing code. The work is closing the distance between specification and evidence.

---

## Core Principles

### 1. The Pyramid Principle

```
Bad Research    = 1000s of bad lines of code
Bad Plan        = 100s of bad lines of code
Bad Code        = 1 bad line of code
```

Focus human effort on **highest leverage** activities: Research and Planning.

### 2. Orchestrator Mindset

Your role shifts from implementation to orchestration:

| Old Role | New Role |
|----------|----------|
| Write code | Direct work |
| Debug manually | Verify output |
| Remember context | Document state |
| Work sequentially | Manage parallel streams |

### 3. Signal Over Ceremony

Replace process with evidence:

| Instead of | Use |
|------------|-----|
| Status reports | Telemetry data |
| Ticket counts | Error rates |
| Hours logged | Uncertainty removed |

> "A good week is one where assumptions were invalidated early, cheaply, and without drama."

### 4. The Blindfold Analogy

Development is navigation in the dark:

1. Take a step
2. If wrong, step back
3. Adjust direction
4. Repeat

Not perfect planning, but **relentless correction**.

### 5. Verification Over Trust

AI generates plausible code. Your job is to verify correctness.

After every change:
1. Type check
2. Lint
3. Build
4. Test

---

## Three Laws

### 1. Understand Before Acting

Never modify code you haven't read. Never generate code for systems you don't understand.

### 2. Plan at the Right Level

| Task | Planning |
|------|----------|
| Quick fix (<50 lines) | Just do it |
| Standard task | Brief plan → Execute |
| Multi-phase | Research → Plan → Execute |

### 3. Capture What You Learn

Knowledge not written down will be lost. Every project should contribute to your knowledge base.

---

## When AI Fails

AI will produce wrong code. Plan for it.

**Warning signs:**
- Output "looks right" but you can't explain why
- Multiple attempts without progress
- Context window full
- AI repeating itself

**Recovery:**
1. Stop generating
2. Reset context
3. Write down what you know
4. Try different approach

---

## The Goal

Help developers build better software faster by:

1. Spending time on right problems (not implementation)
2. Building knowledge that compounds
3. Making AI assistance predictable
4. Maintaining quality while increasing speed

---

*"The work is closing the distance between specification and evidence."*
