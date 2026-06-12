---
paths:
  - "**/flows/**"
  - "**/*.flow-meta.xml"
  - "**/*.flow"
---
# Flow Security

> This file extends [common/security.md](../common/security.md) with Salesforce
> Flow specific content.

Flows can run in a context that **bypasses the user's permission boundary**, so security is
about *re-imposing* CRUD, FLS, and sharing — not trusting the declarative layer to enforce
it. Run mode, guest exposure, and any invoked Apex are the surfaces to check.

## Run mode (`<runInMode>`)

- A Flow set to **system context** (`SystemModeWithoutSharing`, or system mode without FLS)
  reads and writes data the running user could never touch in the UI — sharing, CRUD, and
  FLS are skipped. Use the **user's mode** for user-facing and screen Flows; treat system
  mode as a deliberate, justified, commented exception.
- `SystemModeWithoutSharing` additionally ignores sharing rules — reserve it for genuine
  admin/automation needs, never for ordinary screen Flows.

## Guest / community exposure

- Screen Flows reachable from **Experience Cloud / unauthenticated guest** users run as the
  guest user. Confirm the guest profile grants the *minimum* object and field access, and
  that any Get/DML and subflows reachable from the Flow do not read or write beyond it.
- Never rely on hidden screen components or conditional visibility to enforce data
  security — hidden does not mean inaccessible.

## Invoked Apex (invocable actions)

- Invocable Apex classes are entry points: declare an explicit sharing mode (`with sharing`
  unless justified) and enforce CRUD/FLS inside, exactly as for any controller. See
  [rules/sf-apex/security.md](../sf-apex/security.md).
- Validate and bound any input passed from the Flow before using it in SOQL — build dynamic
  queries with bind variables, never string concatenation of Flow values.

## Secrets, IDs, and endpoints

- No hardcoded record IDs, org IDs, API keys, passwords, or session tokens in formulas,
  assignments, constants, or text templates.
- HTTP callouts must use **Named Credentials** for endpoints and auth — not inlined URLs
  with embedded tokens.
- Treat all data flowing in from URL parameters, screen input, platform events, and external
  responses as untrusted; validate before using it in DML or callouts.

## Review Checklist

- [ ] User-facing / screen Flows run in the user's mode; system mode justified
- [ ] `SystemModeWithoutSharing` not used for ordinary screen Flows
- [ ] Guest-exposed Flows scoped to a minimal profile; subflows respect it
- [ ] Invoked Apex declares sharing and enforces CRUD/FLS; inputs bind-bound
- [ ] No hardcoded secrets or record IDs in formulas/assignments/constants
- [ ] Callouts use Named Credentials; untrusted input validated before use
