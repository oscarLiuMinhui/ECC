---
paths:
  - "**/lwc/**/*.js"
  - "**/lwc/**/*.html"
  - "**/lwc/**/*.css"
  - "**/lwc/**/*.js-meta.xml"
---
# LWC Naming & Structure

> Conventions for naming and structuring Lightning Web Component bundles, their public API,
> events, and metadata. Consistent names and a predictable bundle layout keep an org's
> growing component library discoverable, reusable, and deployable.

## Bundle and files

- **Folder and files share the component's camelCase name:** `lwc/contactList/` containing
  `contactList.js`, `contactList.html`, optional `contactList.css`, and
  `contactList.js-meta.xml`. The JS class is PascalCase (`ContactList`).
- **In markup the component is kebab-case with the namespace prefix:** `<c-contact-list>`.
- **Tests live in `__tests__/contactList.test.js`** beside the bundle.
- **One responsibility per bundle.** Split a large component into a parent plus small children
  rather than overloading one bundle.

## Public API and events

- **`@api` property names are camelCase** and describe the data, not the source
  (`recordId`, `selectedIds`, not `data1`). Treat them as read-only inside the component.
- **Event names are lowercase, no `on` prefix, often a single word** (`select`, `rowselect`,
  `valuechange`) — the template handler adds `on` (`onrowselect`). Set `bubbles`/`composed`
  deliberately and document non-default propagation.
- **Dispatch detail is a plain serializable object;** do not leak internal component state.

## Metadata (`*.js-meta.xml`)

- **Set `isExposed` intentionally** and list only the `<targets>` the component actually
  supports (App/Record/Home page, Experience, Flow, utility bar).
- **Expose configurable inputs via `targetConfigs`/property** with clear labels matching the
  `@api` property names.
- **Keep `apiVersion` current and consistent** across the org's bundles.

## Apex controller

- **Name the controller after the component domain** (`ContactController`), `with sharing`,
  with `@AuraEnabled` method names that read as actions/reads (`getContacts`, `saveContact`).

## Review Checklist

- [ ] Folder, files, and class names match the camelCase/PascalCase component name
- [ ] Tests in `__tests__/`; one clear responsibility per bundle
- [ ] `@api` names camelCase and descriptive; treated as read-only
- [ ] Event names lowercase without `on`; `bubbles`/`composed` set deliberately
- [ ] `isExposed`, `<targets>`, and `targetConfigs` accurate and minimal
- [ ] `apiVersion` current; controller named and shared correctly
