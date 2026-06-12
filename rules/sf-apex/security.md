---
paths:
  - "**/*.cls"
  - "**/*.trigger"
  - "**/*.apex"
---
# Apex Security

> This file extends [common/security.md](../common/security.md) with Salesforce Apex
> specific content.

Apex runs in system context by default — it can bypass the org's sharing model, object
permissions (CRUD), and field-level security (FLS). Security in Apex is therefore about
*re-imposing* the user's permission boundary, not just validating input.

## Sharing

- Declare data-touching classes `with sharing` (or `inherited sharing` for library
  classes that should adopt the caller's context). `without sharing` must carry a comment
  justifying why record-level access is intentionally bypassed.
- Controllers behind `@AuraEnabled`/Visualforce default to **system** context — never
  rely on the UI to enforce record visibility.

```apex
public with sharing class AccountController {
    @AuraEnabled(cacheable=true)
    public static List<Account> getMyAccounts() {
        return [SELECT Id, Name FROM Account WITH USER_MODE]; // honors CRUD/FLS + sharing
    }
}
```

## CRUD / FLS Enforcement

Prefer, in order:

1. **`WITH USER_MODE`** on SOQL/DML (modern, enforces CRUD, FLS, and sharing).
2. **`WITH SECURITY_ENFORCED`** on SOQL (enforces CRUD/FLS for fields in the query).
3. **`Security.stripInaccessible(AccessType.READABLE, records)`** to strip fields the
   user cannot see/edit before returning or writing.
4. Explicit `Schema.sObjectType.Account.isUpdateable()` / `isAccessible()` /
   `isCreateable()` / `isDeletable()` checks for dynamic cases.

Never perform DML or return queried data to the user without one of the above.

## SOQL / SOSL Injection

- Use **bind variables** (`:value`) — never string-concatenate user input into a query.
- For unavoidable dynamic SOQL, wrap untrusted fragments in
  `String.escapeSingleQuotes()` and allow-list field/object names.

```apex
// BAD: injectable
String q = 'SELECT Id FROM Account WHERE Name = \'' + userInput + '\'';
// GOOD: bind variable
List<Account> a = [SELECT Id FROM Account WHERE Name = :userInput];
```

## Secrets & Credentials

- No hardcoded passwords, API keys, session IDs, or org/record IDs in Apex.
- Store secrets in **Named Credentials**, **Protected Custom Metadata/Settings**, or the
  encrypted `Crypto`/platform stores — not in `String` constants or `System.debug`.

## Endpoints & Deserialization

- `@RestResource`/`webservice` methods must perform their own authorization checks; the
  endpoint being reachable is not authorization.
- Guard `JSON.deserialize`/`fromJSON` of untrusted payloads with expected types and size
  limits; do not deserialize into arbitrary sObjects from user input.

## Review Checklist

- [ ] Class declares an explicit sharing mode; `without sharing` justified
- [ ] Every SOQL/DML enforces CRUD/FLS (`USER_MODE`/`SECURITY_ENFORCED`/`stripInaccessible`/explicit checks)
- [ ] No string-concatenated dynamic SOQL; bind variables or escaped + allow-listed
- [ ] No hardcoded secrets or record IDs
- [ ] Apex REST/SOAP endpoints authorize the caller
