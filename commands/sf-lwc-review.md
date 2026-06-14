---
description: Comprehensive Salesforce Lightning Web Components review for LWC bundles (js, html, css, js-meta.xml) and their Apex controllers — reactivity correctness, Lightning Web Security/XSS, controller CRUD-FLS/sharing, client performance, data handling, and accessibility/SLDS. Invokes the sf-lwc-reviewer agent.
---

# LWC Review

This command invokes the **sf-lwc-reviewer** agent for comprehensive Salesforce Lightning
Web Components review across the component bundle and the Apex controllers it calls.

## What This Command Does

1. **Identify LWC Changes**: Find modified bundles via `git diff` — `lwc/<component>/`
   folders with `*.js`, `*.html`, `*.css`, `*.js-meta.xml`, plus any `@AuraEnabled` Apex
   class the component calls.
2. **Reactivity Correctness**: Detect in-place mutation that skips re-render, wrong/missing
   decorators (`@api`/`@track`), and expensive getters.
3. **Security Scan**: XSS via `lwc:dom="manual"`/`innerHTML`, controller CRUD-FLS
   (`WITH USER_MODE`/`stripInaccessible`) and sharing, SOQL injection, unsafe
   `cacheable=true`, secrets/endpoints in client code (require Named Credentials).
4. **Performance Review**: Unguarded `renderedCallback`, missing debounce, unbounded lists,
   redundant server round trips, cacheable-where-safe.
5. **Data Handling**: Loading/error states for imperative Apex, wired `error` handling,
   `refreshApex` after DML.
6. **Accessibility & SLDS**: Labels/ARIA/focus, SLDS classes and design tokens over ad-hoc
   CSS, keyboard support.
7. **Generate Report**: Categorize issues by severity (CRITICAL / HIGH / MEDIUM).

## When to Use

Use `/sf-lwc-review` when:
- After building or modifying an LWC bundle or its Apex controller
- Before committing or deploying components
- Reviewing pull requests that contain LWC
- Hardening a component exposed to guest/Experience Cloud users

## Review Categories

### CRITICAL (Must Fix)
- XSS via raw DOM / unsanitized `innerHTML`; secrets or endpoints in client code
- `@AuraEnabled` controller without CRUD-FLS/sharing; SOQL injection; unsafe `cacheable=true`
- In-place mutation that prevents re-render; wrong `@api`/`@wire` usage

### HIGH (Should Fix)
- Unguarded `renderedCallback`; missing debounce; unbounded lists; redundant server calls
- Imperative Apex without loading/error states; ignored wired `error`; missing `refreshApex`

### MEDIUM (Consider)
- Missing labels/ARIA/focus management; ad-hoc CSS over SLDS/design tokens
- Unclear component/property/event naming; missing Jest coverage

## Related

- Agent: `sf-lwc-reviewer`
- Rules: `rules/sf-lwc/performance.md`, `rules/sf-lwc/security.md`, `rules/sf-lwc/naming-conventions.md`
- Skills: `sf-lwc-patterns`, `sf-lwc-data`, `sf-lwc-performance`, `sf-lwc-security`, `sf-lwc-testing`, `sf-lwc-accessibility`
