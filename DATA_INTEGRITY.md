# Data Integrity Guide for AI-Assisted Development

> "If the database doesn't throw, the bug is invisible."

AI-generated code moves fast but often references database columns that don't exist, writes data to one table but reads from another, and trusts that every query returns what the developer intended. BaaS platforms like Supabase, Firebase, and Hasura make this worse — their clients silently return `undefined` for non-existent columns instead of throwing errors. This guide covers the three most dangerous data integrity patterns and how to prevent them.

## The Three Critical Issues

| Issue | Risk Level | How Common | Detection |
|-------|-----------|------------|-----------|
| Ghost Columns | Critical | Very common | Static analysis |
| Write/Read Path Mismatches | Critical | Common | Path tracing |
| Silent Callback Failures | High | Common | Integration tests |

---

## 1. Ghost Columns

### The Problem

BaaS clients don't validate column names at runtime. If you query a column that doesn't exist, you get `undefined` — not an error. This means:

- Supabase `.select("user_name")` returns `undefined` if the column is actually `username` — no error
- Column renames, typos, and copy-paste errors create invisible data loss
- TypeScript types don't help if the generated types are stale or the query uses raw strings
- The app appears to work; the data simply isn't there

```typescript
// ❌ SILENT FAILURE — column "total_amount" doesn't exist, returns undefined
const { data } = await supabase
  .from('orders')
  .select('id, user_id, total_amount')  // actual column: "amount_total"
  .eq('user_id', userId);

// data[0].total_amount === undefined — no error thrown
```

```python
# ❌ SILENT FAILURE — Firebase equivalent
doc = db.collection('orders').document(order_id).get()
total = doc.to_dict().get('total_amount')  # key doesn't exist → None
```

```javascript
// ❌ SILENT FAILURE — Prisma with raw queries
const result = await prisma.$queryRaw`
  SELECT total_amount FROM orders WHERE id = ${orderId}
`;
// Returns empty object for non-existent column — no runtime error
```

### How to Check

**Step 1: Extract** — Scan your codebase for all database query column references.

**Step 2: Snapshot** — Dump your actual schema to a JSON file.

**Step 3: Compare** — Flag any column referenced in code that doesn't exist in the schema.

**Step 4: Suggest** — Use Levenshtein distance to suggest "did you mean?" corrections.

**Scanner pseudocode (generic — adapt for your stack):**

```
1. Find all .from("table").select("col1, col2") patterns
2. Find all .insert({ col1: val }) patterns
3. Find all .eq("col_name", val) filter patterns
4. Find all .update({ col1: val }) patterns
5. Find all .order("col_name") patterns
6. Load schema-snapshot.json
7. For each (table, column) pair → verify column exists in table
8. Report mismatches with "did you mean?" suggestions (Levenshtein distance)
```

### The Fix

**Commit a schema snapshot to your repo and check it in CI:**

```typescript
// ✅ SAFE — Schema snapshot committed to repo, checked in CI
// scripts/schema-snapshot.json
{
  "tables": {
    "orders": ["id", "user_id", "amount_total", "status", "created_at"],
    "users": ["id", "email", "full_name", "role"],
    "shipping_records": ["id", "order_id", "tracking_number", "carrier", "status"]
  },
  "generated_at": "2026-02-12T00:00:00Z"
}
```

```bash
# ✅ Add to CI pipeline — no DB connection needed at build time
npm run audit:columns:check  # compares code refs against snapshot
```

```typescript
// ✅ SAFE — Validate column names at the query layer
function safeSelect(table: string, columns: string[]) {
  const schema = require('./schema-snapshot.json');
  const validColumns = schema.tables[table];

  for (const col of columns) {
    if (!validColumns.includes(col)) {
      throw new Error(`Ghost column "${col}" in table "${table}". Valid: ${validColumns.join(', ')}`);
    }
  }

  return supabase.from(table).select(columns.join(', '));
}
```

### AI Audit Prompt

```
Scan my codebase for every database query (Supabase .select(), .insert(),
.update(), .eq(), .order(), etc.). Extract all table and column references.
Compare against my actual database schema. Flag any column referenced in
code that doesn't exist in the database. For each ghost column, suggest
the closest matching real column name.
```

---

## 2. Write/Read Path Mismatches

### The Problem

Code writes data to Table A but the UI reads from Table B via a join. The write path doesn't create the FK row that the read path expects. This creates a particularly dangerous pattern:

- Data appears to save successfully (the write works)
- But the UI displays empty or missing data (the read expects more)
- No errors in logs — both the write and read succeed individually
- Extremely common after refactoring when tables or joins change

```typescript
// ❌ WRITE PATH — saves order but doesn't create shipping record
const { data: order } = await supabase
  .from('orders')
  .insert({ user_id, product_id, quantity, status: 'confirmed' });

// ❌ READ PATH — expects shipping record via FK join
const { data: orderDetail } = await supabase
  .from('orders')
  .select('*, shipping_records!order_id(tracking_number, carrier)')
  .eq('id', orderId);

// orderDetail.shipping_records === null — no error, just empty
```

```python
# ❌ Firebase equivalent — write and read use different document structures
# WRITE: saves order
db.collection('orders').document(order_id).set({
    'user_id': user_id,
    'product_id': product_id,
    'status': 'confirmed'
})

# READ: expects nested shipping subcollection
order = db.collection('orders').document(order_id).get()
shipping = db.collection('orders').document(order_id)\
    .collection('shipping').get()  # Empty — never created
```

### How to Check — Critical Path Tracing

For each critical flow (payments, signups, purchases), trace the complete data path:

1. What API/function handles the trigger?
2. What tables + columns are WRITTEN?
3. What does the UI READ (including joins)?
4. Do the write columns match what the read expects?

**Use this trace table template:**

```markdown
| Step | Action | Table | Columns Written | Read By (UI) |
|------|--------|-------|-----------------|--------------|
| 1 | Create order | orders | user_id, total, status | Order detail page |
| 2 | ??? | shipping_records | — MISSING — | Order detail page (join) |
| 3 | Update stock | inventory | quantity | Product page |
```

Any row with "MISSING" in the write column is a write/read path mismatch.

### The Fix

```typescript
// ✅ COMPLETE WRITE PATH — creates all records the read path expects
const { data: order } = await supabase
  .from('orders')
  .insert({ user_id, product_id, quantity, status: 'confirmed' })
  .select()
  .single();

// CRITICAL PATH: This write feeds order detail UI via shipping_records!order_id join
await supabase
  .from('shipping_records')
  .insert({ order_id: order.id, status: 'pending' });
```

**Add code comments linking writes to their dependent reads:**

```typescript
// CRITICAL PATH: This write feeds [UI name] via [table]![fk] join
// See docs/audits/CRITICAL_PATH_TRACE.md — Path N, Step M
```

This comment pattern makes it clear that deleting or moving this write will break a specific UI. It turns an invisible dependency into an explicit one.

### AI Audit Prompt

```
For each of these user flows, trace the complete data path:
1. [Your critical flow, e.g., "User completes purchase"]

For each step, tell me:
- What API route handles it?
- What tables and columns are WRITTEN (insert/update)?
- What does the UI READ (select + joins)?
- Are there any joins where the write path doesn't create the FK row?

Output as a table showing write columns vs read columns for each step.
Flag any mismatches where data is read but never written.
```

---

## 3. Silent Callback/Webhook Failures

### The Problem

External services (payment gateways, auth providers, analytics platforms) send callbacks and webhooks. These handlers are the most dangerous code in your system because:

- They reference columns that may have been renamed or moved
- The handler returns `200` (success) but silently fails to process the data
- No error in logs because the BaaS client doesn't throw on undefined columns
- The external service thinks it succeeded — it won't retry
- Failures only surface when a user reports missing data (or worse, missing money)

```typescript
// ❌ SILENT FAILURE — webhook handler references wrong columns
app.post('/webhooks/payment', async (req, res) => {
  const { order_id, status } = req.body;

  await supabase
    .from('orders')
    .update({
      payment_status: status,        // ❌ column is "status", not "payment_status"
      tokens_count: req.body.quantity // ❌ column is "quantity", not "tokens_count"
    })
    .eq('id', order_id);

  res.status(200).send('OK'); // Returns success — payment gateway thinks it worked
});
```

```python
# ❌ SILENT FAILURE — Flask webhook with wrong field names
@app.route('/webhooks/payment', methods=['POST'])
def payment_webhook():
    data = request.json

    db.collection('orders').document(data['order_id']).update({
        'payment_status': data['status'],  # ❌ field is "status", not "payment_status"
        'paid_amount': data['amount']      # ❌ field is "total", not "paid_amount"
    })

    return 'OK', 200  # External service thinks it worked
```

### How to Check

- **Schema validation in tests** — Verify every column name against the schema snapshot
- **Dry-run tests** — Send realistic payloads and verify database state changes
- **Idempotency tests** — Duplicate callbacks shouldn't double-process
- **Error response tests** — Handler should NOT return 200 if processing fails

### The Fix

**Schema-validated integration tests:**

```typescript
// ✅ Integration test that catches ghost columns in webhook handlers
import { describe, it, expect } from 'vitest';
import { readFileSync } from 'fs';

const schema = JSON.parse(readFileSync('scripts/schema-snapshot.json', 'utf-8'));

describe('Payment Webhook', () => {
  it('should reference only valid column names', () => {
    const source = readFileSync('src/webhooks/payment.ts', 'utf-8');

    // Extract all .update({ key: val }) and .select("col") patterns
    const columnRefs = extractColumnReferences(source, 'orders');

    for (const col of columnRefs) {
      expect(
        schema.tables.orders.includes(col),
        `Ghost column "${col}" referenced in webhook — doesn't exist in orders table`
      ).toBe(true);
    }
  });

  it('should handle duplicate webhooks idempotently', async () => {
    const payload = { order_id: 'test-123', status: 'paid', amount: 100 };

    // First call processes
    const res1 = await request(app).post('/webhooks/payment').send(payload);
    expect(res1.status).toBe(200);

    // Second call with same ID should not double-process
    const res2 = await request(app).post('/webhooks/payment').send(payload);
    expect(res2.status).toBe(200);

    // Verify order was only processed once
    const { data } = await supabase.from('orders').select('*').eq('id', 'test-123');
    expect(data.length).toBe(1);
  });

  it('should not return 200 if processing fails', async () => {
    const badPayload = { order_id: 'nonexistent', status: 'paid' };

    const res = await request(app).post('/webhooks/payment').send(badPayload);
    // Webhook should return 4xx/5xx if it can't process
    // Otherwise payment gateway thinks it succeeded
    expect(res.status).toBeGreaterThanOrEqual(400);
  });
});
```

**Proper error handling in webhook handlers:**

```typescript
// ✅ SAFE — Webhook validates processing before returning success
app.post('/webhooks/payment', async (req, res) => {
  try {
    const { order_id, status } = req.body;

    const { data, error } = await supabase
      .from('orders')
      .update({ status })  // ✅ correct column name
      .eq('id', order_id)
      .select();

    if (error) throw error;
    if (!data || data.length === 0) {
      // Order not found — tell the gateway to retry later
      return res.status(404).json({ error: 'Order not found' });
    }

    res.status(200).send('OK');
  } catch (err) {
    console.error('Webhook processing failed:', err);
    res.status(500).json({ error: 'Processing failed' });
  }
});
```

### AI Audit Prompt

```
Review my webhook/callback handlers. For each one:
1. List every database column referenced (.select, .insert, .update, .eq)
2. Verify each column exists in the actual schema
3. Check: does the handler return 200 even if the database operation fails?
4. Check: is the handler idempotent (safe to call twice with same data)?
5. Check: are there try/catch blocks that silently swallow errors?
```

---

## 4. Prevention Checklist

Before deploying, verify:

### Schema Validation
- [ ] Schema snapshot committed to repo (`schema-snapshot.json`)
- [ ] CI check compares code column references against snapshot
- [ ] Snapshot updated when schema changes (migration script triggers update)
- [ ] Ghost column count is zero (or all remaining are documented as non-critical)

### Critical Path Coverage
- [ ] All money/payment flows have write→read trace documented
- [ ] All auth/signup flows have write→read trace documented
- [ ] Every join has a corresponding write in the same transaction
- [ ] Code comments link write operations to dependent read UIs

### Webhook/Callback Resilience
- [ ] Every webhook handler has integration tests
- [ ] Tests validate column names against schema snapshot
- [ ] Handlers are idempotent (safe to receive duplicate callbacks)
- [ ] Handlers don't return 200 if processing fails
- [ ] Error handling doesn't silently swallow failures

### Ongoing Practices
- [ ] Schema snapshot check runs on every PR/build
- [ ] New webhook handlers require integration tests before merge
- [ ] Column renames trigger a full ghost column scan
- [ ] Critical path trace updated when data model changes

---

## AI Prompts for Data Integrity Audits

### Full Ghost Column Scan

```
Scan my codebase for every database query. Extract all table and column
references. Compare against my actual database schema. Flag any column
referenced in code that doesn't exist in the database. For each ghost
column, suggest the closest matching real column name using edit distance.
```

### Critical Path Trace

```
For each user flow I list below, trace the complete write→read data path.
For each step, show what tables/columns are written and what the UI reads
(including joins). Flag any mismatches where data is read but never written.
```

### Webhook Health Check

```
Review all webhook and callback handlers. For each one, verify column
names against the schema, check error handling (does it return 200 on
failure?), and confirm idempotency (safe to call twice with same payload).
```

---

## Key Principles

| Principle | Meaning |
|-----------|---------|
| **If it doesn't throw, test it** | BaaS clients hide errors — you must catch them yourself |
| **Trace the path** | Every write must have a verified read, every read must have a verified write |
| **Schema is source of truth** | Code must match the database, not the other way around |
| **Test the names, not just the logic** | Column name typos are the #1 source of silent failures |
| **Webhooks are blind spots** | External callbacks are the most dangerous code — test them hardest |

---

## Common Data Integrity Mistakes

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| Trusting BaaS column names | No runtime validation | Schema snapshot CI check |
| Writing to Table A, reading from Table B | Refactoring moves queries | Critical path tracing |
| Webhook returns 200 on failure | Default error handling | Explicit error responses |
| Renaming column in DB but not in code | No automated detection | Ghost column scanner |
| Testing logic but not column names | Traditional test mindset | Schema validation tests |
| Stale TypeScript types | Generated types not refreshed | CI type generation + snapshot |
| Missing FK rows in write path | Incomplete transaction | Critical path comments in code |
| Silent `undefined` in UI | BaaS returns undefined, not error | Null checks + logging |

---

## Resources

- [Supabase Schema API](https://supabase.com/docs/guides/api) — Schema introspection and management
- [Firebase Security Rules](https://firebase.google.com/docs/rules) — Data validation at the database level
- [Prisma Schema](https://www.prisma.io/docs/concepts/components/prisma-schema) — Type-safe database access
- [Levenshtein Distance](https://en.wikipedia.org/wiki/Levenshtein_distance) — For "did you mean?" column name suggestions
- [Vitest](https://vitest.dev/) — Fast test runner for schema validation tests
- [Validation Gates](docs/methodology/validation-gates.md) — AI-Framework verification methodology

---

*Remember: If the database doesn't throw an error, it doesn't mean the data is correct.*
