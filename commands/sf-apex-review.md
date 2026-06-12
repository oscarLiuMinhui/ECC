---
description: Comprehensive Salesforce Apex code review for security (CRUD/FLS, sharing, SOQL injection), governor limits/bulkification, trigger design, and test quality. Invokes the sf-apex-reviewer agent.
---

# Apex Code Review

This command invokes the **sf-apex-reviewer** agent for comprehensive Salesforce Apex
review.

## What This Command Does

1. **Identify Apex Changes**: Find modified `.cls` and `.trigger` files via `git diff`
2. **Run Static Analysis**: Execute the Salesforce Code Analyzer (`sf scanner run`) when available
3. **Security Scan**: Check SOQL/SOSL injection, CRUD/FLS enforcement, sharing declarations, hardcoded secrets/IDs
4. **Governor-Limit Review**: Detect SOQL/DML in loops, non-bulkified logic, unbounded queries
5. **Trigger & Error Handling**: Verify one-trigger-per-object + handler framework, recursion control, partial-DML handling
6. **Test Quality**: Assertions, bulk + negative paths, `runAs`, mocked callouts, and the 75% coverage gate
7. **Generate Report**: Categorize issues by severity (CRITICAL / HIGH / MEDIUM)

## When to Use

Use `/sf-apex-review` when:
- After writing or modifying Apex (classes, triggers, batch/queueable jobs, controllers)
- Before committing or deploying Apex changes
- Reviewing pull requests with Apex code
- Hardening an org for a production deployment (75% coverage gate)

## Review Categories

### CRITICAL (Must Fix)
- SOQL/SOSL injection via string-concatenated queries
- Missing CRUD/FLS enforcement (`USER_MODE`/`SECURITY_ENFORCED`/`stripInaccessible`)
- Missing/`without sharing` on user-data classes without justification
- SOQL or DML inside loops; non-bulkified triggers
- Hardcoded secrets or record IDs

### HIGH (Should Fix)
- Business logic in trigger bodies instead of handler classes
- Missing recursion control; multiple triggers per object
- Swallowed exceptions; unhandled partial DML; DML-before-callout
- Tests without assertions, bulk coverage, or negative paths

### MEDIUM (Consider)
- `System.debug` in production paths; magic values; over-broad `global`/`without sharing`
- Non-idiomatic naming and structure

## Related

- Agent: `sf-apex-reviewer`
- Rules: `rules/sf-apex/security.md`, `rules/sf-apex/governor-limits.md`
- Skills: `sf-apex-testing`, `sf-apex-bulkification`
