---
description: Comprehensive Salesforce Flow review for record-triggered, screen, scheduled, platform-event, and autolaunched Flows — performance under governor limits, run-mode/FLS security, fault-path error handling, declarative-vs-Apex design, trigger order, and naming. Invokes the sf-flow-reviewer agent.
---

# Flow Review

This command invokes the **sf-flow-reviewer** agent for comprehensive Salesforce Flow
review across record-triggered, screen, scheduled, platform-event, and autolaunched Flows.

## What This Command Does

1. **Identify Flow Changes**: Find modified metadata via `git diff` — the `flows/` folder
   and `*.flow-meta.xml` (XML). Read each Flow's `<processType>` and `<start>` block to
   determine its type.
2. **Performance & Governor Limits**: Detect DML or Get Records inside `<loops>`, unbounded
   Get Records, and after-save Flows doing same-record updates that belong in before-save.
3. **Security Scan**: Run mode (`<runInMode>`) that bypasses sharing/CRUD/FLS, guest-user
   screen Flows, invoked-Apex sharing/CRUD-FLS and bind-variable use, hardcoded secrets/IDs,
   Named Credentials.
4. **Error Handling**: Fault connectors on every Get/DML/callout/invocable; fault paths that
   surface or log rather than swallow.
5. **Design Review**: Entry conditions and `recordTriggerType` scoping, before-save vs
   after-save, trigger order across Flows/Apex, subflow modularization, declarative-vs-Apex.
6. **Naming**: Clear element/variable API names, element and Flow descriptions.
7. **Generate Report**: Categorize issues by severity (CRITICAL / HIGH / MEDIUM).

## When to Use

Use `/sf-flow-review` when:
- After building or modifying record-triggered, screen, scheduled, or autolaunched Flows
- Before committing or deploying Flow metadata
- Reviewing pull requests that contain Flows
- Hardening Flows exposed to guest/community users

## Review Categories

### CRITICAL (Must Fix)
- DML or Get Records inside a Loop; unbounded Get Records
- After-save Flow doing a same-record update that belongs in before-save
- Run mode bypassing sharing/CRUD/FLS on user-facing Flows; over-broad guest exposure
- Invoked Apex without sharing/CRUD-FLS; dynamic SOQL from Flow input; hardcoded secrets/IDs
- Missing fault connector on Get/DML/callout/invocable; fault path that swallows the error

### HIGH (Should Fix)
- No entry conditions / no `recordTriggerType` scoping (re-fires, recursion)
- Multiple record-triggered Flows on the same object/event with no defined order
- Monolithic Flows that should be subflows; algorithmic logic that belongs in Apex

### MEDIUM (Consider)
- Unclear element/variable API names; missing element or Flow descriptions
- Inconsistent label/API-name conventions across the Flow library

## Related

- Agent: `sf-flow-reviewer`
- Rules: `rules/sf-flow/performance.md`, `rules/sf-flow/security.md`, `rules/sf-flow/error-handling.md`, `rules/sf-flow/naming-conventions.md`
- Skills: `sf-flow-patterns`, `sf-flow-bulkification`, `sf-flow-error-handling`, `sf-flow-testing`
