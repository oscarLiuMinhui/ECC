---
paths:
  - "**/*.cls"
  - "**/*.trigger"
  - "**/*.apex"
---
# Apex Governor Limits

> This file extends [common/performance.md](../common/performance.md) with Salesforce
> Apex specific content.

Salesforce is multi-tenant: governor limits are enforced **per transaction** and exceeding
one throws an uncatchable `LimitException` that rolls back the transaction. Code must be
written to scale from 1 to 200+ records without approaching these limits.

## Key Per-Transaction Limits (synchronous)

| Resource | Limit |
|----------|-------|
| SOQL queries | 100 |
| Records retrieved by SOQL | 50,000 |
| DML statements | 150 |
| Records processed by DML | 10,000 |
| CPU time | 10,000 ms |
| Heap size | 6 MB (12 MB async) |
| Callouts | 100 |

Async (Batch/Queueable/`@future`) raises some limits but adds its own (e.g. callout and
heap ceilings). Never assume "async makes limits go away."

## The Cardinal Rules

### Never query or DML inside a loop

```apex
// BAD: one SOQL + one DML per iteration -> blows limits at scale
for (Account a : accounts) {
    List<Contact> cs = [SELECT Id FROM Contact WHERE AccountId = :a.Id];
    update cs;
}

// GOOD: bulk query once, DML once
Map<Id, Account> byId = new Map<Id, Account>(accounts);
List<Contact> toUpdate = [SELECT Id, AccountId FROM Contact WHERE AccountId IN :byId.keySet()];
for (Contact c : toUpdate) { /* mutate */ }
update toUpdate;
```

### Bulkify every entry point

Triggers, `@InvocableMethod`, batch `execute`, and controllers all receive **collections**.
Write logic against the whole collection — never `Trigger.new[0]` or single-record
assumptions. See the [bulkification](../../skills/bulkification/SKILL.md) skill.

### Query selectively and bound results

- Filter with indexed fields; add `LIMIT` where large data volumes are possible.
- Select only the fields you use (heap + CPU).
- Use relationship sub-queries / maps instead of N+1 query patterns.

### Move heavy work async

- Use `Queueable`/`Batchable` for large data volumes (chunked in batches of up to 2,000).
- Use `@future(callout=true)` or Queueable for callouts that follow DML (avoids
  "uncommitted work pending").
- Respect async stacking limits (chained Queueables, batch concurrency).

## Review Checklist

- [ ] No SOQL inside loops
- [ ] No DML inside loops
- [ ] Entry points bulkified (operate on collections, tested at ~200 records)
- [ ] Queries filtered/limited; only needed fields selected
- [ ] Large data volumes handled via Batch/Queueable, not synchronous loops
- [ ] No DML-before-callout ordering issues
