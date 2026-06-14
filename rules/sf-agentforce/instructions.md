---
paths:
  - "**/aiAuthoringBundles/**"
  - "**/*.agent"
  - "**/genAiPlugins/**"
  - "**/genAiFunctions/**"
  - "**/genAiPromptTemplates/**"
---
# Agentforce Instructions

> Natural-language instruction conventions for Agentforce agents. The reasoning engine routes
> and parameterizes from this text, so instructions are functional contracts, not documentation.

Agent Script system/reasoning instructions, subagent scope, action "when to use", and
input/output descriptions are all consumed by the Atlas reasoning engine. Vague or contradictory
instructions cause misrouting and wrong parameters — defects, not style nits.

## Subagent scope

- State plainly **what the subagent is for**, with representative example utterances.
- Keep scopes **disjoint** — no two subagents should be able to claim the same utterance.
- Include an explicit **"do not" / hand-off** boundary and a **fallback** for no match.

## Action "when to use"

- Name the **single situation** that should trigger the action (and, where useful, when not to).
- **Describe every input and output** — required vs optional, expected format. The engine fills
  inputs from these descriptions.
- Keep each action **single-purpose**; do not rely on a mode flag the engine must guess.

## System & reasoning instructions

- Give **bounded** step ordering and escalation rules; avoid open-ended "be helpful" prose that
  lets the engine wander.
- State **tone** and any compliance constraints once, at the agent level.
- Specify what to do when **no record / no match / missing input** — ask to clarify, never invent.

## Hygiene

- Prefer short, declarative sentences; one instruction per line where possible.
- Avoid contradictions between agent-level, subagent, and action instructions.
- Re-verify terminology and API expectations after Salesforce releases (`/sf-agentforce-refresh`).

## Review Checklist

- [ ] Subagent scope is concrete, with example utterances and a "do not" boundary
- [ ] Each action names a single triggering situation
- [ ] Every action input/output is described (required/optional, format)
- [ ] Reasoning instructions are bounded, not open-ended
- [ ] A fallback / clarify path exists for no-match and missing input
- [ ] No contradictions across agent / subagent / action instructions
