---
paths:
  - "**/lwc/**/*.js"
  - "**/lwc/**/*.html"
  - "**/lwc/**/*.css"
  - "**/lwc/**/*.js-meta.xml"
---
# LWC Security

> This file extends [common/security.md](../common/security.md) with Salesforce Lightning
> Web Components specific content.

An LWC runs in the user's browser, so the client is never a trust boundary. Anything the
component receives is visible to the user, and any Apex it calls must re-enforce CRUD, FLS,
and sharing on the server. Lightning Web Security (LWS) sandboxes the component but does not
check your data access for you.

## Client trust

- **Never ship secrets to the browser.** No API keys, tokens, or hardcoded credentials in JS
  or static resources. Client-side validation is for UX only; re-validate on the server.
- **Do not return data the UI merely hides.** If a field/record must not be seen, do not send
  it to the client; a hidden element is not access control.

## Controller (Apex) boundary

- **Enforce CRUD/FLS** in every `@AuraEnabled` method: query `WITH USER_MODE` or apply
  `Security.stripInaccessible` to results and inputs.
- **Default to `with sharing`;** justify any `without sharing` class exposed to a component.
- **Parameterize SOQL/SOSL** with bind variables; never concatenate method arguments.
  Whitelist any dynamic field/object names.
- **Mark only side-effect-free reads `cacheable=true`;** never cache DML/callout methods or
  sensitive/volatile data.

## DOM and rendering

- **Prefer data-bound templates** (LWC auto-escapes). Use `lwc:dom="manual"`/`innerHTML` only
  when unavoidable, and **sanitize** the content; never inject raw user/record markup or build
  URLs from untrusted input.

## Callouts and exposure

- **External callouts go through Apex + Named/External Credentials,** not client JS; add
  endpoints to CSP Trusted Sites where the page loads them.
- **Review guest/Experience Cloud exposure:** public components run with minimal privileges,
  so verify object/field access rather than trusting the UI to restrict it.

## Review Checklist

- [ ] No secrets/keys/credentials in client JS or static resources
- [ ] `@AuraEnabled` controller enforces CRUD/FLS (`WITH USER_MODE`/`stripInaccessible`)
- [ ] Controller `with sharing` unless `without sharing` is justified
- [ ] SOQL/SOSL parameterized; dynamic names whitelisted
- [ ] `lwc:dom="manual"`/`innerHTML` sanitized; no raw untrusted markup
- [ ] `cacheable=true` only on non-sensitive, side-effect-free reads
- [ ] Callouts via Apex + Named Credentials; CSP Trusted Sites configured
- [ ] Guest/Experience access verified against object/field permissions
