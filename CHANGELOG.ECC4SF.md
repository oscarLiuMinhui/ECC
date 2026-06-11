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

- `apex-reviewer` agent — Salesforce Apex code review covering security (SOQL/SOSL
  injection, CRUD/FLS, sharing), governor limits and bulkification, trigger-handler
  framework, and test quality (75% deploy-coverage gate).
- `apex-testing` skill — patterns to meet the 75% Apex coverage gate with real
  assertions, `TestDataFactory`/`@testSetup`, bulk (200-record) and negative paths,
  `System.runAs()` permission tests, and mocked callouts.
- `bulkification` skill — write Apex that scales 1→200+ records per transaction:
  SOQL/DML out of loops, collection-based logic, maps/relationship queries.
- `rules/apex/security.md` — sharing, CRUD/FLS enforcement
  (`USER_MODE`/`SECURITY_ENFORCED`/`stripInaccessible`), SOQL injection, secrets.
- `rules/apex/governor-limits.md` — per-transaction limits, no SOQL/DML in loops,
  bulkified entry points, async for large data volumes.
- `/apex-review` command — invokes the `apex-reviewer` agent; registered in
  `agent.yaml` and the command registry.

### Fixed

- Inherited unicode-safety gate failure: replaced U+2605/U+2606 star-glyph
  ratings with plain-ASCII `N/5` in `windows-desktop-e2e` SKILL.md and its ja-JP
  mirror, so `check-unicode-safety.js` passes.

### Infrastructure

- Established ECC4SF as a rebranded downstream distribution of upstream ECC with an
  upstream sync channel — see [docs/ECC4SF-FORK-STRATEGY.md](docs/ECC4SF-FORK-STRATEGY.md).
- Branch topology: `vendor/ecc` (upstream mirror) -> `staging` (integration) -> `main`.
- CI validation gate extended to run on the `staging` branch.
