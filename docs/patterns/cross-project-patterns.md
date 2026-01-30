# Cross-Project Patterns

Learnings that transfer between projects.

These patterns were extracted from multiple production projects and apply regardless of specific technology choices.

---

## Database Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| RLS policy ordering | Supabase multi-tenant | Always check `user_id` or `org_id` first in WHERE clause |
| Migration rollback | Any SQL database | Always write DOWN migrations, test both directions |
| Soft delete | User-facing data | Add `deleted_at` column, filter in all queries |
| Idempotent migrations | CI/CD environments | Use `IF NOT EXISTS`, avoid breaking on re-run |
| Connection pooling | High-traffic apps | Pool at app level (20-50), not per request |
| Cursor pagination | Large datasets | Use cursor-based (faster) over offset (simpler) at scale |
| Enum columns | Status fields | Use database enums, not strings — prevents typos |

---

## Frontend Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| RTL support | Internationalized apps | Use logical properties (`ps`/`pe` not `pl`/`pr` in Tailwind) |
| Form validation | Any user input | Validate client-side AND server-side — never trust client |
| Loading states | Async operations | Always show skeleton/spinner, never blank screen |
| Optimistic updates | Fast UI | Update UI immediately, rollback on server error |
| Error boundaries | React apps | Wrap routes in error boundaries, show recovery UI |
| Hydration warnings | SSR apps | Use `suppressHydrationWarning` for time-based content |

---

## AI Workflow Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| Context pollution | Long sessions | Write PROGRESS.md after each phase, start fresh at 60% |
| Circular errors | Agent loops 3+ times | Stop immediately, review the plan — something is wrong |
| Missing context | Implementation fails | Research didn't include file paths with line numbers |
| Plan rejection | AI disagrees with approach | Plans are easier to fix than code — iterate on plan first |
| "Different approach" | AI says this phrase | Red flag — save progress and start fresh context |
| Spec-first | Any feature | Write spec before code — specs are the source of truth |

---

## API Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| Error responses | REST APIs | Always include `{ error: { code, message } }` |
| Pagination | List endpoints | Cursor-based > offset-based at scale |
| Rate limiting | Public APIs | Return `Retry-After` header with 429 |
| Idempotency | Write operations | Accept `Idempotency-Key` header, store results |
| Versioning | Breaking changes | Use URL versioning (`/v1/`, `/v2/`) not headers |
| Health checks | Production APIs | Test DB, cache, and dependencies — not just "200 OK" |

---

## Security Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| Token storage | Web apps | httpOnly cookies > localStorage (XSS protection) |
| Input validation | User data | Whitelist allowed values > blacklist dangerous ones |
| Secrets | Environment | Never commit, use `.env.local`, rotate regularly |
| CORS | API security | Explicit allowlist, never `*` in production |
| Webhook verification | External events | Verify signatures, check timestamps (prevent replay) |
| SQL injection | Database queries | Use parameterized queries, never string concatenation |

---

## DevOps Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| Zero-downtime deploy | Production | Blue-green or rolling deploys, never stop-start |
| Correlation IDs | Distributed systems | Generate at edge, propagate through all services |
| Structured logging | Observability | JSON logs with consistent fields, not free-text |
| Circuit breakers | External dependencies | Fail fast when downstream is unhealthy |
| Feature flags | Gradual rollout | Ship behind flag, enable for % of users |
| Rollback plan | Every deploy | Know how to rollback before you deploy |

---

## Testing Patterns

| Pattern | Context | Solution |
|---------|---------|----------|
| Test data in migrations | Database tests | Seed in migrations, not separate scripts |
| Real devices | Mobile apps | Simulators hide performance issues — test on device |
| Integration over unit | Business logic | Test behavior, not implementation details |
| Snapshot sparingly | UI tests | Only for stable components — high maintenance cost |
| Mock external only | Test isolation | Mock APIs/services, not internal modules |

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails | Better Approach |
|--------------|--------------|-----------------|
| `SELECT *` in RLS | Causes infinite recursion | Select specific columns |
| Transactions with external calls | Network delays cause lock contention | Call externally after commit |
| Storing tokens in localStorage | XSS can steal tokens | httpOnly cookies |
| Offset pagination at scale | Performance degrades with page number | Cursor pagination |
| Free-text error messages | Hard to handle programmatically | Error codes + messages |
| "It works on my machine" | Environment differences | Containerize, test in CI |

---

## How to Use This Document

1. **Before starting a project**: Review patterns relevant to your tech stack
2. **When stuck**: Check if an anti-pattern applies
3. **After learning something**: Add it here for future projects
4. **During code review**: Reference patterns to explain decisions

---

*Add your own patterns as you learn them. This document should grow with experience.*
