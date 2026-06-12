---
paths:
  - "**/flows/**"
  - "**/*.flow-meta.xml"
  - "**/*.flow"
---
# Flow Naming & Maintainability

> Conventions for naming and documenting Flows and their elements. Clear, consistent names
> and descriptions are what keep an org's growing automation library understandable,
> reviewable, and deployable.

## Flow naming

- **Flow API name** should encode object + purpose, e.g. `Opportunity_SetDefaults_BeforeSave`
  or `Account_CreateRenewalTask_AfterSave`. Make the type/trigger legible from the name.
- Keep a consistent convention across the library (object-first, action verb, trigger
  suffix) so related automations sort and read together.
- Always fill in the Flow **description** — what it does, when it runs, and why.

## Element & variable naming

- Give every element a clear **API name** that says what it does (`Get_OpenCases`,
  `Update_Owner`, `Loop_Through_Items`) — not `Get_Records_0` / `Decision_2`.
- Name variables and collections by what they hold (`openCases`, `toUpdate`, `productById`),
  with a consistent case convention.
- Add **element descriptions** on non-obvious Decisions, Assignments, and Get filters so a
  reviewer understands intent without reverse-engineering the XML.

## Subflows & structure

- Name autolaunched subflows by their single responsibility and stable input/output contract
  so they are discoverable and reusable (see
  [sf-flow-patterns](../../skills/sf-flow-patterns/SKILL.md)).
- Keep one trigger framework / a defined `triggerOrder` per object so the automation set is
  predictable.

## Versioning & activation

- Flows are **versioned**; only one version is active. Save changes as a new version and
  activate deliberately — verify the active version is the intended one before release.
- Deploy a Flow together with the invocable Apex / objects / fields it depends on so the
  active set is internally consistent in the target org.

## Review Checklist

- [ ] Flow API name encodes object + purpose + trigger; description filled in
- [ ] Elements and variables have meaningful API names, not defaults
- [ ] Non-obvious elements carry descriptions explaining intent
- [ ] Subflows named by responsibility with a clear input/output contract
- [ ] The intended Flow version is the active one; dependencies deployed together
