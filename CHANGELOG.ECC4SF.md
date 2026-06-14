# ECC4SF Changelog

Salesforce-specific (ECC4SF) changes only. Upstream ECC changes are tracked in
[CHANGELOG.md](CHANGELOG.md).

This file is **additive and ECC4SF-owned** — upstream never edits it, so it stays
conflict-free across upstream syncs. Add user-facing Salesforce changes to
`## Unreleased`; promote to a dated, versioned section (`ecc4sf-<semver>`) at release.

Format follows [Keep a Changelog](https://keepachangelog.com/). The raw per-commit
log lives in git history (conventional commits); this is the curated human log.

## Unreleased

### Added

- **LWC pack** — full build-and-review pack for Lightning Web Components (the bundle:
  `*.js`, `*.html`, `*.css`, `*.js-meta.xml`, plus the Apex controllers they call):
  - `sf-lwc-reviewer` agent — reviews LWC bundles for reactivity correctness (in-place
    mutation that skips re-render, `@api`/`@wire` misuse), Lightning Web Security/XSS,
    controller CRUD-FLS/sharing and SOQL injection, client performance (`renderedCallback`
    guards, debounce, unbounded lists, round trips), data handling (loading/error states,
    `refreshApex`), and accessibility/SLDS.
  - `sf-lwc-builder` agent — scaffolds a complete bundle (reactive JS, SLDS/a11y HTML,
    `*.js-meta.xml`), a secure `with sharing` Apex controller, and a Jest test.
  - `/sf-lwc-review` and `/sf-lwc-build` commands — invoke the agents; registered in
    `agent.yaml` and the command registry.
  - `sf-lwc-patterns` skill — composition/slots, `@api`/getter reactivity, immutable state
    updates, parent-child events, Lightning Message Service, splitting components.
  - `sf-lwc-data` skill — Lightning Data Service vs `@wire` vs imperative Apex,
    `cacheable=true` semantics, `refreshApex`, loading/error states, GraphQL wire.
  - `sf-lwc-performance` skill — re-render hygiene, `renderedCallback` guards, debounce,
    pagination/lazy-load, round-trip minimization, leak cleanup in `disconnectedCallback`.
  - `sf-lwc-security` skill — LWS, XSS/`lwc:dom` sanitization, controller `WITH USER_MODE`/
    `stripInaccessible`/sharing, Named Credentials, keeping secrets/PII off the client.
  - `sf-lwc-testing` skill — `sfdx-lwc-jest`: `createElement`, mocking wire adapters and
    imperative Apex, async DOM assertions, event and error-path coverage.
  - `sf-lwc-accessibility` skill — base Lightning components, labels/ARIA/focus, keyboard
    nav, SLDS design tokens over hardcoded styling, responsive grid.
  - `rules/sf-lwc/` — `performance.md`, `security.md`, `naming-conventions.md` (path-scoped
    to `lwc/` bundle files).
- **Flow pack** — full review pack covering record-triggered (before/after-save), screen,
  scheduled, platform-event, and autolaunched Flows:
  - `sf-flow-reviewer` agent — reviews Flow metadata for performance under governor limits
    (no Get/DML inside Loops, before-save over after-save, bounded Get Records), security
    (run mode / FLS bypass, guest-user screen Flows, invoked-Apex sharing/CRUD-FLS),
    fault-path error handling, declarative-vs-Apex design, trigger order, and naming.
  - `/sf-flow-review` command — invokes the `sf-flow-reviewer` agent; registered in
    `agent.yaml` and the command registry.
  - `sf-flow-patterns` skill — choosing the Flow type, before-save vs after-save, entry
    conditions and trigger order, subflow decomposition, and Flow-vs-Apex.
  - `sf-flow-bulkification` skill — keeping Flows within per-transaction governor limits:
    never Get/DML inside a Loop, assign-in-loop then one DML, bounded Get Records,
    delegating heavy volume to bulk-safe Apex.
  - `sf-flow-error-handling` skill — fault connectors on every Get/DML/callout/invocable,
    surfacing/logging instead of swallowing, Custom Error, rollback semantics, bounded retry.
  - `sf-flow-testing` skill — Flow Tests for record-triggered Flows, debug runs, Apex
    coverage for invoked invocables (75% gate), bulk and fault-path coverage.
  - `rules/sf-flow/` — `performance.md`, `security.md`, `error-handling.md`,
    `naming-conventions.md` (path-scoped to `flows/` and `*.flow-meta.xml`).
- **OmniStudio pack** — full review pack covering OmniScripts, Integration Procedures,
  DataRaptors, and FlexCards:
  - `sf-omnistudio-reviewer` agent — reviews OmniStudio metadata for performance under
    governor limits (no server work in Loop Blocks, bulk/Turbo DataRaptors, trimmed
    responses), security (DataRaptor FLS, guest exposure, invoked-Apex sharing/CRUD-FLS),
    declarative-vs-Apex design, versioning/activation, and naming.
  - `/sf-omnistudio-review` command — invokes the `sf-omnistudio-reviewer` agent;
    registered in `agent.yaml` and the command registry.
  - `sf-omnistudio-patterns` skill — DataRaptor vs IP vs Apex choice, modularizing
    monolithic OmniScripts/IPs into reusable sub-components, FlexCard composition, and
    versioning/activation discipline.
  - `sf-omnistudio-performance` skill — keeping IPs/DataRaptors within per-transaction
    governor limits: no server work inside Loop Blocks, bulk DataRaptors, Turbo Extracts,
    minimal round trips, response trimming, caching, delegating volume to bulk-safe Apex.
  - `sf-omnistudio-data-mapping` skill — DataRaptor Extract/Turbo/Transform/Load choice,
    FLS enforcement, bounded queries, bound inputs, and bulk-safe mappings.
  - `sf-omnistudio-testing` skill — DataRaptor/IP preview test cases, Apex coverage for
    invoked Remote Actions (75% gate), and Jest for FlexCard/OmniStudio LWC.
  - `rules/sf-omnistudio/performance.md`, `security.md`, `naming-conventions.md`.
- `sf-apex-reviewer` agent — Salesforce Apex code review covering security (SOQL/SOSL
  injection, CRUD/FLS, sharing), governor limits and bulkification, trigger-handler
  framework, and test quality (75% deploy-coverage gate).
- `sf-apex-testing` skill — patterns to meet the 75% Apex coverage gate with real
  assertions, `TestDataFactory`/`@testSetup`, bulk (200-record) and negative paths,
  `System.runAs()` permission tests, and mocked callouts.
- `sf-apex-bulkification` skill — write Apex that scales 1→200+ records per transaction:
  SOQL/DML out of loops, collection-based logic, maps/relationship queries.
- `rules/sf-apex/security.md` — sharing, CRUD/FLS enforcement
  (`USER_MODE`/`SECURITY_ENFORCED`/`stripInaccessible`), SOQL injection, secrets.
- `rules/sf-apex/governor-limits.md` — per-transaction limits, no SOQL/DML in loops,
  bulkified entry points, async for large data volumes.
- `/sf-apex-review` command — invokes the `sf-apex-reviewer` agent; registered in
  `agent.yaml` and the command registry.

### Changed

- Adopted a uniform `sf-` namespace prefix for all ECC4SF components (see
  [docs/ECC4SF-FORK-STRATEGY.md](docs/ECC4SF-FORK-STRATEGY.md) §10). Renamed the
  initial Apex pack: `apex-reviewer`→`sf-apex-reviewer`, `apex-testing`→
  `sf-apex-testing`, `bulkification`→`sf-apex-bulkification`, `rules/apex/`→
  `rules/sf-apex/`, `/apex-review`→`/sf-apex-review`. Technology stays in the name
  (`sf-<tech>-<role>`) so the family is grouped and collision-safe against upstream.

### Fixed

- Inherited unicode-safety gate failure: replaced U+2605/U+2606 star-glyph
  ratings with plain-ASCII `N/5` in `windows-desktop-e2e` SKILL.md and its ja-JP
  mirror, so `check-unicode-safety.js` passes.

### Infrastructure

- Established ECC4SF as a rebranded downstream distribution of upstream ECC with an
  upstream sync channel — see [docs/ECC4SF-FORK-STRATEGY.md](docs/ECC4SF-FORK-STRATEGY.md).
- Branch topology: `vendor/ecc` (upstream mirror) -> `staging` (integration) -> `main`.
- CI validation gate extended to run on the `staging` branch.
