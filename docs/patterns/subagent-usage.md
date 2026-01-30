# Subagent Usage

Subagents are spawned AI sessions. Use them for **context control**, not role-play.

## Purpose

When your main agent needs to search or find information, spawning a subagent keeps your primary context clean. The subagent does the searching, returns a summary, and its context is discarded.

## When to Use Subagents

| ✅ Good Use | ❌ Bad Use |
|-------------|-----------|
| "Find all files related to authentication" | "Act as a senior developer" |
| "Search for where payments are processed" | "Be a code reviewer persona" |
| "Locate the database schema for users" | "Pretend you're a security expert" |

## How It Works

```
┌─────────────────────────────────────┐
│  MAIN AGENT                         │
│  - Maintains clean context          │
│  - Focuses on implementation        │
│  - Receives summaries, not raw data │
└─────────────────────────────────────┘
            │
            │ "Find where auth happens"
            ▼
┌─────────────────────────────────────┐
│  SUBAGENT                           │
│  - Searches 100 files               │
│  - Reads 50 functions               │
│  - Context gets polluted            │
│  - Returns: "auth.ts:45-67"         │
│  - Context discarded ✓              │
└─────────────────────────────────────┘
```

## Ideal Subagent Response

A good subagent response is concise with exact locations:

```markdown
## Auth System Location

| File | Lines | Purpose |
|------|-------|---------|
| `src/lib/auth.ts` | 45-67 | Token validation |
| `src/lib/auth.ts` | 89-102 | Session creation |
| `src/app/api/login/route.ts` | 12-34 | Login endpoint |

## Data Flow
User → LoginForm → /api/login → auth.ts:validateToken() → DB
```

## Key Insight

> "Subagents are really about context control... The parent model can get right to work without having to have the context burden of all of that reading and searching."
> — Dexter Horthy

## Anti-Patterns

- ❌ Using subagents for "persona" role-play
- ❌ Having subagents return full file contents
- ❌ Spawning subagents for simple tasks
- ❌ Not specifying what format you want back

---

*Next: [Context Warning Signs](context-warning-signs.md)*
