# ECC4SF Fork & Branch Strategy

> Decision record for building **ECC4SF** (ECC for Salesforce) as a rebranded,
> independent distribution of upstream **ECC** (`affaan-m/ECC`) that still
> tracks upstream improvements.
>
> Status: **Accepted** · Created: 2026-06-11 · Owner: oscarLiuMinhui

## 1. Context

This repo (`oscarLiuMinhui/ECC`) is a fork of [`affaan-m/ECC`](https://github.com/affaan-m/ECC),
a harness-native agent operating system (`ecc-universal` v2.0.0). Upstream is
actively maintained.

Goal: create **ECC4SF**, a Salesforce-focused product, while continuing to
absorb upstream ECC improvements and security fixes.

## 2. Decisions

| # | Decision | Rationale |
|---|----------|-----------|
| D1 | **Rebranded independent distribution**, not a one-off snapshot | Own product identity (ECC4SF) while still benefiting from upstream maintenance. |
| D2 | **Keep syncing upstream long-term** via a dedicated `upstream` remote | Stay current on fixes and new harness support. |
| D3 | **Additive-only Salesforce content** — new files, never edits to shared files | New files never conflict on merge; this is what makes D1 + D2 coexist. |
| D4 | **Surgical branding** confined to a known short list of files | Minimizes recurring merge conflicts to a predictable surface. |
| D5 | **Preserve the canonical directory layout** (`agents/ skills/ commands/ rules/ scripts/`) | Keeps upstream merges clean and lets ECC's validators enforce our SF content for free. |
| D6 | **Own version line** (`ecc4sf-*` tags) | Avoids colliding with upstream semver (`v2.x`). |

### Branding surface (the only shared files we edit)

- `package.json` → `name`
- `README.md` → hero / title
- `.claude-plugin/marketplace.json`
- `AGENTS.md` → header

Expect to re-resolve **only these** on each upstream sync.

## 3. Branch topology

```
upstream (affaan-m/ECC)
        │ fetch only
        ▼
  vendor/ecc ──────────────►  staging  ──────────►  main
  (pure mirror,            (integration:         (ECC4SF stable /
   ff-only, never edit)     upstream + SF work,   releasable product)
        ▲                    tested here)              ▲
        │                        ▲                     │
   git fetch upstream            │              reviewed merges only
                          sf/<feature> branches
```

| Branch | Role | Rule |
|--------|------|------|
| `vendor/ecc` | Clean mirror of `upstream/main` | **Fast-forward only. Never commit here.** |
| `staging` | Integration: upstream merges + SF feature work, tested | Default working/integration branch. |
| `main` | ECC4SF stable / releasable product | Only receives reviewed merges from `staging`. |
| `sf/<topic>` | Feature branches | Branch off `staging`, PR back into `staging`. |

Promotion flow: `sf/<topic>` → `staging` → `main`.
Upstream always enters through `staging` first, so `main` stays releasable.

## 4. Remotes

| Remote | URL | Access |
|--------|-----|--------|
| `origin` | `https://github.com/oscarLiuMinhui/ECC.git` | read/write — hosts ECC4SF |
| `upstream` | `https://github.com/affaan-m/ECC.git` | fetch only |

## 5. One-time setup

```bash
git remote add upstream https://github.com/affaan-m/ECC.git
git fetch upstream
git branch vendor/ecc upstream/main      # clean mirror
git checkout -b staging                  # integration branch off main
# (push when ready) git push -u origin staging
```

## 6. Recurring upstream sync

```bash
git fetch upstream
git checkout vendor/ecc && git merge --ff-only upstream/main   # mirror; never conflicts
git checkout staging   && git merge vendor/ecc                 # resolve branding-only conflicts
npm test                                                       # full gate must pass
npm run catalog:sync && npm run command-registry:write         # counts drift after merges
git checkout main      && git merge --no-ff staging            # promote when green
```

## 7. CI

`.github/workflows/ci.yml` triggers on `main` + `release/**` (push) and `main`
(PR). Add `staging` so PRs into it run the full validation gate:

```yaml
on:
  push:
    branches: [main, staging, 'release/**']
    tags: ['v*']
  pull_request:
    branches: [main, staging]
```

## 8. Versioning

- Upstream uses `v2.x` semver — do not reuse.
- ECC4SF releases tag as `ecc4sf-<semver>` (e.g. `ecc4sf-0.1.0`).

## 8a. Change logging

Layered, no per-change files:

- **Per-change:** conventional commits (commitlint-enforced) — the raw, attributed log.
- **Curated human log:** `CHANGELOG.ECC4SF.md` (ECC4SF-owned, additive → conflict-free
  on upstream syncs). Add user-facing Salesforce changes under `## Unreleased`; promote
  to a dated `ecc4sf-<semver>` section at release. Do **not** edit upstream's shared
  `CHANGELOG.md` for ECC4SF changes.
- **Decisions:** `docs/` records (like this file) for architectural decisions only.

## 9. Principles adopted from ECC

ECC's validation gate (`scripts/ci/validate-*.js`) already enforces house style,
so conforming SF content inherits the discipline automatically.

| ECC principle | ECC4SF / Salesforce translation |
|---|---|
| Agent-First delegation | `apex-reviewer`, `lwc-reviewer`, `soql-optimizer`, `flow-reviewer`, `sf-deploy-resolver` agents |
| Test-Driven, coverage-gated (80% JS) | Salesforce mandates 75% Apex coverage to deploy — encode in `apex-testing` skill + rule |
| Security-First (Prompt Defense Baseline in every rule) | CRUD/FLS, `WITH SECURITY_ENFORCED`, SOQL-injection, sharing → `apex-security` rule |
| Immutability / efficiency | Bulkification + governor limits (no SOQL/DML in loops) → `bulkification` skill |
| Plan Before Execute | Keep `/plan`; valuable for multi-object deployments |
| Canonical source → adapter fan-out | Keep SF content in canonical dirs; existing build fans it out per harness |
| Catalog as source of truth | Run `catalog:sync` after adding SF content |
| Conventions (lowercase-hyphen, YAML frontmatter, conventional commits) | `apex-reviewer.md`, `feat(sf): ...`; validators enforce |
| Skill format (When to Use / How It Works / Examples) | Same three sections for every SF skill |

### First Salesforce additions (all new files → zero merge conflict)

- **Agents:** `apex-reviewer`, `lwc-reviewer`, `soql-optimizer`, `flow-reviewer`, `sf-deploy-resolver`
- **Skills:** `apex-patterns`, `apex-testing`, `lwc-jest-testing`, `trigger-handler-framework`, `bulkification`, `sfdx-source-deploy`, `scratch-org-workflow`
- **Rules:** `apex-security`, `apex-style`, `governor-limits` (each with the Prompt Defense Baseline header)
- **Commands:** `/sf-deploy`, `/apex-test`, `/apex-review`, `/lwc-test`, `/soql-check`

Base each new file on the closest existing peer (e.g. `apex-reviewer` on
`rust-reviewer`) so it passes the validators on first run.
