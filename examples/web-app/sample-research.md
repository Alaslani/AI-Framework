# Research: Cost Anomaly Detection

> **Date**: 2026-01-29
> **Status**: Complete
> **Next**: Plan phase

---

## Questions Answered

1. **How does the current cost system work?**
2. **Where is cost data stored?**
3. **What patterns exist for anomaly detection?**

---

## File Map

| File | Lines | Purpose |
|------|-------|---------|
| `src/lib/cost/fetcher.ts` | 1-89 | Fetches raw cost data from cloud APIs |
| `src/lib/cost/fetcher.ts` | 45-67 | Rate limiting logic |
| `src/lib/cost/normalizer.ts` | 1-120 | Normalizes multi-cloud cost formats |
| `src/lib/cost/normalizer.ts` | 78-95 | Currency conversion |
| `src/server/routers/cost.ts` | 1-156 | tRPC procedures for cost queries |
| `src/server/routers/cost.ts` | 89-120 | `getCostByService` procedure |
| `supabase/migrations/20260115_costs.sql` | 1-45 | Cost table schema |
| `src/components/dashboard/CostChart.tsx` | 1-89 | Cost visualization component |

---

## Data Flow

```
Cloud APIs (AWS/GCP/Azure)
        │
        ▼
┌─────────────────────────┐
│  fetcher.ts             │ ← Cron job every hour
│  - Rate limited         │
│  - Retry with backoff   │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  normalizer.ts          │
│  - Unified format       │
│  - Currency conversion  │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  Supabase: costs table  │
│  - org_id (RLS)         │
│  - service, region      │
│  - amount_usd           │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  cost.ts (tRPC)         │
│  - getCostByService     │
│  - getCostTrend         │
└─────────────────────────┘
        │
        ▼
┌─────────────────────────┐
│  CostChart.tsx          │
│  - Recharts line chart  │
└─────────────────────────┘
```

---

## Key Functions

| Function | Location | Purpose |
|----------|----------|---------|
| `fetchCostData()` | fetcher.ts:12 | Entry point for cost fetching |
| `normalizeAwsCost()` | normalizer.ts:23 | AWS → unified format |
| `normalizeGcpCost()` | normalizer.ts:56 | GCP → unified format |
| `convertCurrency()` | normalizer.ts:78 | Multi-currency support |
| `getCostByService` | cost.ts:89 | tRPC procedure for service breakdown |

---

## Existing Patterns

### Rate Limiting (fetcher.ts:45-67)
```typescript
const rateLimiter = new RateLimiter({
  tokensPerInterval: 10,
  interval: 'second'
});
```
**Use**: Apply same pattern for anomaly detection API calls.

### RLS Policy (migrations/20260115_costs.sql:34-42)
```sql
CREATE POLICY "Users can view own org costs"
ON costs FOR SELECT
USING (org_id = auth.jwt() ->> 'org_id');
```
**Use**: Anomaly table needs same RLS pattern.

---

## Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| recharts | 2.12.0 | Cost visualization |
| date-fns | 3.3.0 | Date calculations |
| limiter | 2.1.0 | Rate limiting |

---

## Constraints

- **Cloud API rate limits**: AWS (10 req/s), GCP (5 req/s), Azure (8 req/s)
- **Cost data delay**: Up to 24 hours from cloud providers
- **RLS required**: All queries must go through Supabase policies

---

## Open Questions for Planning

1. What algorithm for anomaly detection? (z-score, IQR, ML-based?)
2. How far back should baseline look? (7 days, 30 days, 90 days?)
3. Real-time or batch detection?
4. Where to store anomaly results?

---

## Checkpoint

**Understanding correct?**
- [ ] Cost flow: Cloud → Fetcher → Normalizer → DB → tRPC → UI
- [ ] RLS enforced at database level
- [ ] Hourly cron job updates costs
- [ ] Need new table for anomalies

---

*Research complete. Ready for planning phase.*
