---
paths:
  - "**/lwc/**/*.js"
  - "**/lwc/**/*.html"
  - "**/lwc/**/*.css"
  - "**/lwc/**/*.js-meta.xml"
---
# LWC Performance

> This file extends [common/performance.md](../common/performance.md) with Salesforce
> Lightning Web Components specific content.

LWC performance is client-side: the work happens in the user's browser. Slowness comes from
re-rendering too often, calling the server too often, rendering too much DOM, and leaking.
The framework re-renders on reassignment of reactive fields, so disciplined state updates
are also a performance concern, not only a correctness one.

## Rendering

- **Reassign reactive fields with new references; do not churn.** In-place mutation does not
  re-render (a bug), but reassigning unchanged data every cycle also wastes diffs. Update only
  on real change.
- **Guard `renderedCallback`.** It runs after every render. Never set reactive state in it
  unconditionally (render loop); use a one-time flag for DOM setup and read, don't write,
  state there.
- **Keep getters cheap.** Getters run on render; no Apex, heavy loops, or new-object churn.
- **Give `for:each`/`iterator` items a stable `key`** so the diff is efficient.

## Server round trips

- **Debounce keystroke-driven Apex** (search/filter) ~250-300 ms; clear the timer in
  `disconnectedCallback`.
- **Batch related reads** into one wire/Apex call instead of several sequential calls.
- **Prefer `@AuraEnabled(cacheable=true)` reads** so the wire/LDS cache de-dupes repeats; reuse
  data already in memory instead of re-querying.
- **Call `refreshApex` after writes** rather than re-fetching everything from scratch.

## DOM and data volume

- **Paginate or lazy-load large lists** (`lightning-datatable` server paging, load-on-scroll).
  Do not render thousands of rows into the DOM.
- **Trim payloads.** Select only the fields the component renders; large records inflate
  client memory and serialization.

## Lifecycle and leaks

- **Clean up in `disconnectedCallback`:** clear `setTimeout`/`setInterval`, remove
  `window`/`document` listeners, unsubscribe from Lightning Message Service. Leaked
  subscriptions keep components alive and degrade the page over time.

## Delegating to Apex

When data volume or computation exceeds what is reasonable on the client, do set-based work
in a bulk-safe, governor-aware controller (see the
[sf-apex-bulkification](../../skills/sf-apex-bulkification/SKILL.md) skill) and return only
what the component needs. Moving work server-side still runs under per-transaction limits.

## Review Checklist

- [ ] Reactive fields reassigned only on real change; getters cheap, no churn
- [ ] `renderedCallback` guarded; no unconditional state writes inside it
- [ ] Keystroke-driven Apex debounced; timer cleared on disconnect
- [ ] Related reads batched; cacheable reads used; `refreshApex` after writes
- [ ] Large lists paginated/lazy-loaded; payload fields trimmed
- [ ] Timers, listeners, and LMS subscriptions cleaned up on disconnect
