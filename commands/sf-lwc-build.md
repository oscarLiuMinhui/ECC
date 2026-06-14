---
description: Scaffold a complete Salesforce Lightning Web Component — bundle (js, html, css, js-meta.xml), a secure with-sharing Apex controller, and a Jest test — following LWC reactivity, security, performance, and accessibility conventions. Invokes the sf-lwc-builder agent.
---

# LWC Build

This command invokes the **sf-lwc-builder** agent to scaffold a complete, convention-correct
Lightning Web Component and the secure Apex it depends on — so you can make custom UI and
functions that pass `/sf-lwc-review` on the first try.

## What This Command Does

1. **Establish the Contract**: Component name (camelCase), purpose, target surface
   (App/Record/Home page, Experience site, Flow, utility bar), `@api` inputs, and data
   source (LDS / wire Apex / imperative Apex / GraphQL).
2. **Choose Data Strategy**: Lightning Data Service vs wire Apex vs imperative Apex per the
   `sf-lwc-data` skill; prefer LDS and `cacheable=true` reads where appropriate.
3. **Scaffold the Bundle** under `lwc/<component>/`:
   - `<component>.js` — `@api` properties, reactive state by reassignment, `@wire`/imperative
     Apex with loading + error state, `connectedCallback`/`disconnectedCallback` cleanup,
     `CustomEvent` dispatch.
   - `<component>.html` — SLDS markup, accessible labels/ARIA, `lwc:if`/`for:each` with keys,
     spinner and error region.
   - `<component>.css` — only if needed; SLDS utilities and design tokens.
   - `<component>.js-meta.xml` — `apiVersion`, `isExposed`, `<targets>`/`targetConfigs`.
   - `__tests__/<component>.test.js` — Jest with mocked wire/Apex and DOM assertions.
4. **Scaffold the Apex Controller** (when a server function is needed): `with sharing`,
   `@AuraEnabled(cacheable=true)` only for side-effect-free reads, `WITH USER_MODE` and bind
   variables, Named Credentials for callouts, bulk-safe DML.
5. **Report**: List files created and remaining manual steps (deploy, assign to page or
   permission set).

## When to Use

Use `/sf-lwc-build` when:
- Creating a new Lightning Web Component from a description
- Adding a data-backed component (record list, form, dashboard tile) with its controller
- Scaffolding a component plus its Jest test and Apex in one pass

## Related

- Agent: `sf-lwc-builder`
- Review: `/sf-lwc-review` (run after building)
- Rules: `rules/sf-lwc/performance.md`, `rules/sf-lwc/security.md`, `rules/sf-lwc/naming-conventions.md`
- Skills: `sf-lwc-patterns`, `sf-lwc-data`, `sf-lwc-performance`, `sf-lwc-security`, `sf-lwc-testing`, `sf-lwc-accessibility`
