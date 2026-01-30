# CloudDash Master Reference v2.3

**Version**: 2.3 | **Date**: 2026-01-30 | **Status**: Production

---

## Quick Reference

| Item | Value |
|------|-------|
| Repository | https://github.com/company/clouddash |
| Production | https://app.clouddash.io |
| Staging | https://staging.clouddash.io |
| Database | Supabase project `clouddash-prod` |
| Docs | https://docs.clouddash.io |

### Key Commands

```bash
# Development
pnpm dev

# Build
pnpm build

# Test
pnpm test

# Deploy
vercel --prod

# Database migrations
pnpm db:migrate
```

---

## Architecture Overview

CloudDash is a multi-tenant SaaS dashboard for cloud infrastructure monitoring. Users connect AWS/GCP/Azure accounts and view unified metrics, alerts, and cost analysis.

### Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | Next.js 14, React 18, Tailwind CSS, shadcn/ui |
| Backend | Next.js API Routes, tRPC |
| Database | Supabase (PostgreSQL + RLS) |
| Auth | Supabase Auth (OAuth + Magic Link) |
| Hosting | Vercel (frontend), Supabase (backend) |
| Monitoring | Sentry, Posthog |

### Key Patterns

- **Row Level Security (RLS)** — All data access through Supabase policies
- **Server Components** — Default to RSC, client components only when needed
- **Feature Flags** — LaunchDarkly for gradual rollouts
- **Optimistic Updates** — UI updates before server confirmation

---

## Key Decisions

| # | Decision | Rationale | Date |
|---|----------|-----------|------|
| 1 | Supabase over Firebase | Need PostgreSQL + RLS for multi-tenancy | 2026-01-05 |
| 2 | tRPC over REST | Type safety, better DX, smaller bundle | 2026-01-08 |
| 3 | shadcn/ui over Chakra | Customizable, no runtime CSS-in-JS | 2026-01-10 |
| 4 | Cursor-based pagination | Better performance at scale than offset | 2026-01-15 |
| 5 | httpOnly cookies for tokens | More secure than localStorage | 2026-01-18 |

---

## Learnings

| # | Learning | Category |
|---|----------|----------|
| 1 | RLS policies need SECURITY DEFINER for cross-table joins | Gotcha |
| 2 | Foreign keys must reference `auth.users`, not custom tables | Gotcha |
| 3 | Use `useOptimistic` for instant UI feedback | Pattern |
| 4 | Don't use `SELECT *` in RLS policies — causes recursion | Anti-pattern |
| 5 | Server Actions need `revalidatePath` after mutations | Gotcha |
| 6 | Supabase realtime requires explicit channel subscription | Gotcha |
| 7 | Use `suppressHydrationWarning` for time-based content | Pattern |
| 8 | tRPC procedures should return typed errors, not throw | Pattern |
| 9 | Always seed test data in migrations, not separate scripts | Pattern |
| 10 | RTL support: use `ps`/`pe` not `pl`/`pr` in Tailwind | Pattern |

---

## External Integrations

| Service | Purpose | Docs |
|---------|---------|------|
| AWS | Cloud metrics API | Internal wiki |
| GCP | Cloud metrics API | Internal wiki |
| Stripe | Billing & subscriptions | stripe.com/docs |
| Resend | Transactional email | resend.com/docs |
| Sentry | Error tracking | sentry.io/docs |

---

## Current Focus

### Active Work
- Multi-region deployment (Phase 2 of 4)
- Cost anomaly detection feature

### Recent Completions
- User impersonation for support (2026-01-28)
- Slack integration for alerts (2026-01-25)

### Upcoming
- SOC 2 compliance audit prep
- Mobile app (React Native)

---

## Cross-References

| Document | Purpose |
|----------|---------|
| TRANSFER_PACK.md | Session handoff |
| plans/ | Feature implementation plans |
| research/ | Technical research documents |
| .github/CODEOWNERS | Code review assignments |

---

*Updated: 2026-01-30*
