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
