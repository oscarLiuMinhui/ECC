---
paths:
  - "**/aiEvaluationDefinitions/**"
  - "**/aiAuthoringBundles/**"
  - "**/*.agent"
  - "**/genAiPlugins/**"
  - "**/genAiFunctions/**"
---
# Agentforce Testing & Evals

> This file extends [common/testing.md](../common/testing.md) with Salesforce Agentforce
> specific content.

Agent behavior is probabilistic, so it is validated with **evals** (`AiEvaluationDefinition`),
not unit tests. Each test case maps an utterance (plus optional context variables) to expected
subagent and action sequences. Evals run in the Testing Center or via `sf agent test run` and
should gate activation.

## Coverage

- **Every subagent and action** has at least one eval case asserting it is the expected
  routing/action target.
- **Assert the subagent + action sequence** (and inputs), not only response text — that catches
  misrouting directly.
- **Context-variable variations**: test the same utterance under different channels/user types/
  record contexts.

## Adversarial & negative

- **Out-of-scope** utterances hit the fallback / hand-off, not a data action.
- **Prompt-injection** and **data-exfil** attempts are refused / stay in scope.
- **No-record / ambiguous / missing-input** cases assert "ask to clarify", never invention.

## Lifecycle

- Deploy the `AiEvaluationDefinition` (`sf project deploy start`) and run `sf agent test run`
  **before** `sf agent publish` / `sf agent activate`.
- Treat a **pass-rate drop** after editing instructions, scope, or grounding as a blocking
  regression.
- Do not test against production; use a deployed eval definition.

## Review Checklist

- [ ] Every subagent and action has an eval case
- [ ] Expectations assert subagent + action sequence, not only text
- [ ] Context-variable variations covered
- [ ] Out-of-scope, injection, and data-exfil cases present
- [ ] Negative/empty/ambiguous cases assert clarify, not invention
- [ ] Evals run via `sf agent test run` and gate activation
