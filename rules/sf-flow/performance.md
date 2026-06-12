---
paths:
  - "**/flows/**"
  - "**/*.flow-meta.xml"
  - "**/*.flow"
---
# Flow Performance

> This file extends [common/performance.md](../common/performance.md) with Salesforce
> Flow specific content.

Flow is declarative, but it runs on the same multi-tenant platform as Apex. A
record-triggered Flow processes records in **batches of up to 200**, and every Flow executes
inside a transaction bound by the **same per-transaction governor limits** (100 SOQL,
150 DML, 10,000 ms CPU, 6 MB heap). A "no-code" element that queries or commits per record
fails at scale exactly like SOQL/DML in an Apex loop. See
[governor-limits.md](../sf-apex/governor-limits.md) for the limit table.

## Loops

- **No DML element inside a Loop.** A Create/Update/Delete Records element in a `<loops>`
  block issues one DML per iteration — the Flow DML-in-a-loop. Assign each record to a
  collection variable in the loop, then do **one** DML on the collection after the loop.
- **No Get Records inside a Loop.** Do one bulk Get **before** the loop and match/filter the
  result in memory; never query per iteration.

## Get Records

- **Bound every Get.** Add filter conditions on indexed fields and a row limit; an
  unfiltered Get over a large object hits the 50,000-row retrieval ceiling and CPU limit.
- **Select only the fields you use** — fewer fields means less heap and serialization.
- **Set "first record" vs "all records" deliberately**; pull a collection only when you
  iterate it.

## Record-triggered design

- **Prefer before-save for same-record field updates.** A before-save Flow sets the field
  with **no DML** and runs ~10x faster than the equivalent after-save Update Records.
- **Scope the trigger.** Use `recordTriggerType` and entry conditions / "only when changed"
  so the Flow runs only when it must and doesn't re-fire on unrelated updates (recursion,
  wasted limits).
- **Order multiple automations.** Set `triggerOrder` when several record-triggered Flows run
  on the same object/event so behavior is deterministic alongside Apex triggers.

## Delegating to Apex

When data volume or logic exceeds what Flow elements handle within limits, call a bulk-safe
invocable Apex action (see the
[sf-apex-bulkification](../../skills/sf-apex-bulkification/SKILL.md) skill). Moving work to
Apex does **not** escape the per-transaction limits.

## Review Checklist

- [ ] No DML element inside any Loop (assign-in-loop, DML once after)
- [ ] No Get Records inside any Loop (one bulk Get before)
- [ ] Every Get filtered, limited, and selecting only used fields
- [ ] Same-record updates done in before-save, not after-save
- [ ] Triggers scoped with `recordTriggerType` + entry conditions; order defined
- [ ] Heavy / bulk-volume logic delegated to bulk-safe invocable Apex
