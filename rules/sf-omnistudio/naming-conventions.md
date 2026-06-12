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
# OmniStudio Naming & Versioning

> Conventions for naming, JSON structure, and version/activation hygiene across OmniStudio
> components. Consistent names and disciplined activation are what keep an org's growing
> library of OmniScripts, IPs, DataRaptors, and FlexCards maintainable and deployable.

## Component naming

- **OmniScripts / Integration Procedures** are identified by a `Type` and `SubType`
  (e.g. `Account/CreateContact`). Use a consistent, domain-first `Type` and an
  action/verb `SubType`; keep them stable — renaming breaks references from FlexCards,
  Apex, and other IPs.
- **DataRaptors**: name by interface and intent — prefix by direction
  (`Extract`/`Transform`/`Load`/`Turbo`) and object, e.g. `ExtractAccountForCard`,
  `LoadOrderItems`. The name should make the data flow obvious without opening it.
- **FlexCards**: name by the entity and context they render (`AccountSummaryCard`), and
  keep child/parent card names related so the composition is discoverable.

## JSON node naming (data contract)

- Element names become JSON node keys in the OmniScript/IP data JSON — they are the
  **data contract** between elements, DataRaptors, and the consuming UI. Use clear,
  stable, camelCase-or-domain names; do not rename casually once downstream elements,
  formulas, or DataRaptor mappings reference them.
- Group related values under structured nodes rather than a flat soup of top-level keys;
  it makes Set Values, conditional views, and DataRaptor input/output mappings legible.
- Keep merge-field paths (`%node%`, `{node}`) consistent with the element names that
  produce them.

## Versioning & activation

- OmniStudio components are **versioned**; only one version is active at a time. Increment
  a new version for changes; never edit and re-activate in place when other components
  depend on a known-good version.
- **Reference active versions.** A FlexCard or IP that points at an inactive/draft
  component fails at runtime — verify the referenced Type/SubType/version is active before
  release.
- Deactivate superseded versions deliberately and confirm nothing still references them.
- Deploy components together with their dependencies (IP -> its DataRaptors -> invoked
  Apex) so the active set is internally consistent in the target org.

## Review Checklist

- [ ] Type/SubType and DataRaptor/FlexCard names follow the domain-first convention
- [ ] JSON node / element names are clear, structured, and stable
- [ ] Changes ship as a new version, not an in-place edit of a depended-on version
- [ ] All referenced components are the active version
- [ ] Component deployed with its DataRaptor / Apex dependencies
