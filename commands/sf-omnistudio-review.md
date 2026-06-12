---
description: Comprehensive Salesforce OmniStudio review for OmniScripts, Integration Procedures, DataRaptors, and FlexCards — performance under governor limits, DataRaptor FLS/security, declarative-vs-Apex design, versioning/activation, and naming. Invokes the sf-omnistudio-reviewer agent.
---

# OmniStudio Review

This command invokes the **sf-omnistudio-reviewer** agent for comprehensive Salesforce
OmniStudio review across OmniScripts, Integration Procedures, DataRaptors, and FlexCards.

## What This Command Does

1. **Identify OmniStudio Changes**: Find modified metadata via `git diff` — OmniScripts,
   Integration Procedures, DataRaptors, FlexCards (`omniscripts/`,
   `integrationprocedures/`, `dataraptors/`, `flexcards/`, typically JSON).
2. **Performance & Governor Limits**: Detect server work inside IP Loop Blocks, per-record
   DataRaptor calls, unbounded Extracts, and excessive client round trips.
3. **Security Scan**: DataRaptor FLS, guest/community exposure, invoked-Apex sharing/CRUD-FLS
   and bind-variable use, hardcoded secrets/IDs, Named Credentials.
4. **Design Review**: Declarative-vs-Apex placement, modularization of monolithic IPs/
   OmniScripts, trimmed responses.
5. **Versioning & Activation**: References to active versions, new-version discipline,
   dependencies shipped together.
6. **Naming**: Stable Type/SubType, DataRaptor/FlexCard names, and clear JSON node naming.
7. **Generate Report**: Categorize issues by severity (CRITICAL / HIGH / MEDIUM).

## When to Use

Use `/sf-omnistudio-review` when:
- After building or modifying OmniScripts, Integration Procedures, DataRaptors, or FlexCards
- Before committing or deploying OmniStudio metadata
- Reviewing pull requests that contain OmniStudio components
- Hardening components exposed to guest/community users

## Review Categories

### CRITICAL (Must Fix)
- DataRaptor / HTTP / Remote Action inside an IP Loop Block; per-record DataRaptor calls
- Unbounded DataRaptor Extracts; excessive server round trips
- DataRaptor FLS disabled on user data; over-broad guest/community exposure
- Invoked Apex without sharing/CRUD-FLS; SOQL concatenated from OmniScript input
- Hardcoded secrets or record IDs; endpoints without Named Credentials

### HIGH (Should Fix)
- Logic that belongs in Apex (or vice versa); monolithic, non-modular IPs/OmniScripts
- Untrimmed IP/DataRaptor responses
- References to inactive versions; in-place edits of depended-on versions; dependencies not shipped together

### MEDIUM (Consider)
- Unclear/unstable Type/SubType, DataRaptor, or FlexCard names
- Flat or ambiguous JSON node naming; inconsistent merge-field paths

## Related

- Agent: `sf-omnistudio-reviewer`
- Rules: `rules/sf-omnistudio/performance.md`, `rules/sf-omnistudio/security.md`, `rules/sf-omnistudio/naming-conventions.md`
- Skills: `sf-omnistudio-patterns`, `sf-omnistudio-performance`, `sf-omnistudio-data-mapping`, `sf-omnistudio-testing`
