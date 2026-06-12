---
paths:
  - "**/omniscripts/**"
  - "**/integrationprocedures/**"
  - "**/dataraptors/**"
  - "**/flexcards/**"
  - "**/*.omniscript"
  - "**/*.integrationprocedure"
  - "**/*.dataraptor"
---
# OmniStudio Performance

> This file extends [common/performance.md](../common/performance.md) with Salesforce
> OmniStudio specific content.

OmniStudio is declarative, but it runs on the same multi-tenant platform as Apex:
Integration Procedures (IPs) and DataRaptors execute inside a transaction bound by the
**same per-transaction governor limits** (100 SOQL, 150 DML, 10,000 ms CPU, 6 MB heap).
A "no-code" element that loops or queries per record fails at scale exactly like SOQL in
an Apex loop. See [governor-limits.md](../sf-apex/governor-limits.md) for the limit table.

## Integration Procedures

- **No element inside a Loop Block that issues server work.** A DataRaptor Extract, HTTP
  Action, or Remote Action placed inside a `Loop Block` runs once per iteration — the
  OmniStudio equivalent of SOQL-in-a-loop. Gather keys, make **one** bulk call, then map
  results in a List Action / Set Values.
- **Prefer one bulk DataRaptor over N single-record ones.** Pass a list of keys and let a
  single Extract return all rows.
- **Use Turbo (Standard Runtime) Extracts** for read-only DataRaptor Extracts — they skip
  the field-mapping engine and are markedly faster on large result sets.
- **Minimize round trips.** Each Integration Procedure Action / Remote Action from an
  OmniScript or FlexCard is a server call. Consolidate dependent reads into one IP rather
  than chaining many client-initiated calls.
- **Trim the response.** Use `Additional Output` / `Send/Response Action` to return only
  the nodes the consumer needs — large JSON inflates heap and view-layer cost.
- **Set `Chainable` / `Use Future` deliberately.** Long-running IPs invoked from UI should
  run async where the UX allows, not block the OmniScript.

## DataRaptors

- **Bound every Extract.** Add filters and limits; an unfiltered Extract over a large
  object hits the 50,000-row retrieval ceiling and the CPU limit.
- **Select only the fields you map.** Extra fields cost heap and serialization time.
- **Push set-based work to the query**, not to Transform formulas evaluated per row.
- **Batch Loads.** A DataRaptor Load processes its input as a set — feed it the full
  collection rather than calling it per record from a Loop Block.

## FlexCards

- **Avoid per-card data sources in a repeating/list context** — one data source feeding
  the list beats one call per rendered card.
- **Cache where data is stable** (FlexCard data-source caching / IP response caching) to
  avoid re-fetching on every render.

## Delegating to Apex

When data volume or logic exceeds what declarative elements handle within limits, call a
Remote Action (invoked Apex) — and make that Apex bulk-safe and governor-aware (see the
[sf-apex-bulkification](../../skills/sf-apex-bulkification/SKILL.md) skill). Moving work to
Apex does **not** escape the per-transaction limits.

## Review Checklist

- [ ] No DataRaptor / HTTP / Remote Action inside an IP Loop Block
- [ ] Single bulk DataRaptor instead of per-record Extracts
- [ ] Extracts filtered, limited, and select only mapped fields
- [ ] Turbo Extract used for read-only reads where eligible
- [ ] Server round trips from OmniScript/FlexCard minimized and responses trimmed
- [ ] Heavy / large-volume logic delegated to bulk-safe invoked Apex
