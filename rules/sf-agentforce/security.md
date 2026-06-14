---
paths:
  - "**/aiAuthoringBundles/**"
  - "**/*.agent"
  - "**/genAiPlannerBundles/**"
  - "**/genAiPlugins/**"
  - "**/genAiFunctions/**"
  - "**/genAiPromptTemplates/**"
  - "**/*.bot-meta.xml"
---
# Agentforce Security & Guardrails

> This file extends [common/security.md](../common/security.md) with Salesforce Agentforce
> specific content.

An Agentforce agent processes untrusted user utterances and feeds instructions, grounding, and
action results into an LLM. The model context is a data-exposure boundary, the reasoning engine
will not enforce access for you, and a crafted utterance can attempt to pull the agent out of
scope. Defend at the subagent scope, the action boundary, and the data merged into prompts.

## LLM context exposure

- **Never put secrets in instructions.** No API keys, tokens, or credentials in Agent Script,
  subagent, or prompt-template text — instructions enter the model context.
- **Actions return the minimum.** A `GenAiFunction` (Apex/Flow) must return only the fields the
  agent needs, redacted — never raw records, full field sets, or internal IDs into the LLM.
- **Ground with the minimum.** Merge only needed fields into prompt templates; respect FLS so the
  running user cannot be shown fields they lack access to.

## Action (Apex/Flow) boundary

- **Enforce CRUD/FLS** in every invoked Apex action: query `WITH USER_MODE` or apply
  `Security.stripInaccessible`. The agent runs as a user; the action must enforce access.
- **Default to `with sharing`;** justify any `without sharing` action.
- **Parameterize SOQL/SOSL** with bind variables; never concatenate utterance-derived inputs.
- **Callouts via Named/External Credentials,** never credentials in action code or instructions.

## Scope & prompt-injection guardrails

- **Every subagent has an explicit "do not" boundary** and hand-off — the first defense against
  scope drift and injection.
- **Delimit untrusted text** (utterances, case comments, retrieved/external content) as data, not
  instructions, so it cannot override the system prompt (indirect injection).
- **Provide a fallback** for unmatched utterances (clarify / escalate); never improvise outside
  scope.
- **Cover guardrails with evals** — out-of-scope, injection, and data-exfil cases must be tested
  before activation.

## Review Checklist

- [ ] No secrets/keys/credentials in any instruction or prompt text
- [ ] Actions return minimal, redacted fields (no raw PII/secrets to the LLM)
- [ ] Apex actions enforce CRUD/FLS (`WITH USER_MODE`/`stripInaccessible`) and `with sharing`
- [ ] SOQL/SOSL parameterized; no concatenated utterance input
- [ ] Every subagent has a "do not" boundary and a fallback
- [ ] Untrusted/grounded text delimited as data, not instructions
- [ ] Injection / out-of-scope / data-exfil evals exist
