---
paths:
  - "**/omniscripts/**"
  - "**/integrationprocedures/**"
  - "**/dataraptors/**"
  - "**/flexcards/**"
  - "**/*.omniscript"
  - "**/*.integrationprocedure"
  - "**/*.dataraptor"
---
# OmniStudio Security

> This file extends [common/security.md](../common/security.md) with Salesforce
> OmniStudio specific content.

OmniStudio components run with the permissions of the configured runtime, and several can
bypass the user's permission boundary if built carelessly. As with Apex, security is about
*re-imposing* CRUD, FLS, and sharing — not trusting the declarative layer to do it.

## DataRaptors — CRUD / FLS

- DataRaptors can be configured to run with or without FLS checks. **Enable FLS** ("Check
  Field Level Security" / run in user mode) for any DataRaptor that serves user-facing
  data, so the user only reads/writes fields they are permitted to.
- A DataRaptor Load that skips FLS can write fields the running user could never edit in
  the UI — treat skipped FLS as a deliberate, justified, commented exception.
- Extract only the fields the consumer needs; do not return sensitive fields the UI never
  displays.

## Guest / Community exposure

- OmniScripts and FlexCards exposed to **guest users** (Experience Cloud / unauthenticated
  sites) run as the guest user. Confirm the guest profile grants the *minimum* object and
  field access required, and that DataRaptors/IPs reachable from those components do not
  read or write beyond it.
- Never rely on the OmniScript/FlexCard UI (hidden elements, conditional views) to enforce
  data security — hidden does not mean inaccessible to a crafted request.

## Invoked Apex (Remote Actions)

- Remote Action classes are entry points: declare an explicit sharing mode (`with sharing`
  unless justified) and enforce CRUD/FLS inside, exactly as for any controller. See
  [rules/sf-apex/security.md](../sf-apex/security.md).
- Validate and bound any input passed from the OmniScript JSON before using it in SOQL —
  build queries with bind variables, never string concatenation.

## Secrets, IDs, and endpoints

- No hardcoded record IDs, org IDs, API keys, passwords, or session tokens in OmniScript /
  IP / FlexCard / DataRaptor definitions or formula fields.
- HTTP Actions / Remote calls must use **Named Credentials** for endpoints and auth — not
  inlined URLs with embedded tokens.
- Treat all data flowing in from URL parameters, prefill, and external responses as
  untrusted; validate before mapping it into DML or callouts.

## Review Checklist

- [ ] DataRaptors serving user data enforce FLS; skipped FLS is justified
- [ ] Guest/community-exposed components scoped to a minimal profile
- [ ] Invoked Apex declares sharing and enforces CRUD/FLS; inputs bind-bound
- [ ] No hardcoded secrets or record IDs in any definition or formula
- [ ] External endpoints use Named Credentials, not inlined URLs/tokens
- [ ] Untrusted input (URL params, prefill, responses) validated before use
