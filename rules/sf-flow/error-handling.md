---
paths:
  - "**/flows/**"
  - "**/*.flow-meta.xml"
  - "**/*.flow"
---
# Flow Error Handling

> Conventions for fault handling in Flows. An element that can fail at runtime needs a fault
> path; without one, the failure becomes an unhandled error that shows users a raw fault
> screen and rolls back the transaction.

## Fault connectors

- **Every fallible element gets a fault connector** — Get Records, any Create/Update/Delete,
  an Action/callout, and any invocable Apex action.
- **The fault path must do something useful**: surface a message, log the error, or notify an
  admin. A missing fault path *or* a fault path that goes to an empty/dead-end element both
  hide failures — the second is worse because it looks handled.

## Surfacing the fault

- **Screen Flows** — route the fault to a Screen that shows `{!$Flow.FaultMessage}` and a
  sensible next step; never leave users on the platform's raw "unhandled fault" screen.
- **Record-triggered / autolaunched Flows** — log the fault (custom log object / platform
  event) or send an admin notification; the running user may never see it otherwise.

## Custom errors and rollback

- Use the **Custom Error** element in record-triggered Flows to block an invalid save with a
  clear, validation-style message instead of a raw runtime error.
- An unhandled fault **rolls back the whole transaction**. If the main record should save but
  a side-effect may fail gracefully, isolate the side-effect (async path / separate
  transaction) and handle its fault there.

## Retries

- For transient failures (callouts), implement bounded retries — fault path increments a
  counter, waits, retries up to a limit, then routes to a terminal failure path. No unbounded
  retry loops.

## Review Checklist

- [ ] Every Get / DML / callout / invocable element has a fault connector
- [ ] Fault paths surface a message (screen) or log/notify (autolaunched) — never swallow
- [ ] No empty / dead-end fault connectors that discard `{!$Flow.FaultMessage}`
- [ ] Record-triggered validation uses the Custom Error element with a clear message
- [ ] Rollback is intentional; partial-save side-effects isolated and handled
- [ ] Callout retries are bounded with a terminal failure path
