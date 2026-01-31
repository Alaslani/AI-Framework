# OrderFlow API Master Reference v3.1

**Version**: 3.1 | **Date**: 2026-01-30 | **Status**: Production

---

## Quick Reference

| Item | Value |
|------|-------|
| Repository | https://github.com/company/orderflow-api |
| Production | https://api.orderflow.io |
| Staging | https://staging-api.orderflow.io |
| Database | PostgreSQL on AWS RDS |
| Docs | https://docs.orderflow.io/api |

### Key Commands

```bash
# Development
npm run dev

# Run tests
npm test

# Run specific test
npm test -- --grep "orders"

# Database migrations
npm run migrate

# Generate OpenAPI spec
npm run openapi:generate

# Deploy
npm run deploy:staging
npm run deploy:production
```

---

## Architecture Overview

OrderFlow is a B2B order management API for e-commerce platforms. Handles order lifecycle from creation through fulfillment, with webhooks for real-time updates. Multi-tenant with API key authentication.

### Tech Stack

| Layer | Technology |
|-------|------------|
| Runtime | Node.js 20, TypeScript |
| Framework | Fastify |
| Database | PostgreSQL 15, Drizzle ORM |
| Cache | Redis (ElastiCache) |
| Queue | BullMQ (Redis-backed) |
| Auth | API keys + JWT for webhooks |
| Hosting | AWS ECS Fargate |
| CI/CD | GitHub Actions |

### Key Patterns

- **Repository pattern** — Data access abstracted behind repositories
- **Command/Query separation** — Write operations in services, reads in queries
- **Idempotency keys** — All write endpoints support idempotency
- **Rate limiting** — Per-tenant, tiered by plan
- **Webhook retry** — Exponential backoff with dead letter queue

---

## Key Decisions

| # | Decision | Rationale | Date |
|---|----------|-----------|------|
| 1 | Fastify over Express | 2x faster, better TypeScript support | 2026-01-03 |
| 2 | Drizzle over Prisma | Type-safe, no code generation step | 2026-01-05 |
| 3 | BullMQ over SQS | Redis already in stack, better DX | 2026-01-08 |
| 4 | ECS over Lambda | Long-running connections for webhooks | 2026-01-10 |
| 5 | OpenAPI-first | Spec drives code generation for clients | 2026-01-12 |

---

## Learnings

| # | Learning | Category |
|---|----------|----------|
| 1 | Always return `Retry-After` header on 429 | Pattern |
| 2 | Idempotency keys should be tenant-scoped | Gotcha |
| 3 | Use `FOR UPDATE SKIP LOCKED` for queue processing | Pattern |
| 4 | Webhook signatures must use raw body, not parsed JSON | Gotcha |
| 5 | Connection pooling: 20 per container, not per request | Pattern |
| 6 | Use cursor pagination for list endpoints at scale | Pattern |
| 7 | Always log correlation IDs for distributed tracing | Pattern |
| 8 | Database transactions should not call external APIs | Anti-pattern |
| 9 | Use database enums for status fields, not strings | Pattern |
| 10 | Health check endpoint should test DB and Redis | Pattern |

---

## API Structure

```
/v1
├── /orders
│   ├── POST   /              Create order
│   ├── GET    /              List orders (paginated)
│   ├── GET    /:id           Get order
│   ├── PATCH  /:id           Update order
│   └── POST   /:id/cancel    Cancel order
├── /webhooks
│   ├── POST   /              Register webhook
│   ├── GET    /              List webhooks
│   └── DELETE /:id           Delete webhook
├── /inventory
│   ├── GET    /              Get inventory levels
│   └── POST   /adjust        Adjust inventory
└── /health
    └── GET    /              Health check
```

---

## External Integrations

| Service | Purpose | Docs |
|---------|---------|------|
| Stripe | Payment processing | stripe.com/docs |
| ShipStation | Fulfillment | shipstation.com/docs |
| Segment | Analytics | segment.com/docs |
| PagerDuty | Alerting | pagerduty.com/docs |

---

## Cross-References

| Document | Purpose |
|----------|---------|
| TRANSFER_PACK.md | Session handoff |
| openapi.yaml | API specification |
| RUNBOOK.md | Operational procedures |

---

*Updated: 2026-01-30*
