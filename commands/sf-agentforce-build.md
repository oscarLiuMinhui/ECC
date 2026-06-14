---
description: Scaffold a complete Salesforce Agentforce agent — an AiAuthoringBundle with an Agent Script (*.agent) file, non-overlapping subagents (GenAiPlugin), secure actions (GenAiFunction, with-sharing Apex/Flow/prompt), grounding prompt templates, and a starter AiEvaluationDefinition — following Agentforce DX conventions. Invokes the sf-agentforce-builder agent.
---

# Agentforce Build

This command invokes the **sf-agentforce-builder** agent to scaffold a complete,
convention-correct Agentforce agent using the Agentforce DX pro-code model — so you can build a
custom agent that passes `/sf-agentforce-review` on the first try.

## What This Command Does

1. **Establish the Contract**: Agent type (service / employee / custom), channel (Slack,
   Experience site, internal), and the jobs-to-be-done — each distinct job becomes one subagent
   with a disjoint scope.
2. **Decompose into Subagents**: Non-overlapping subagents per the `sf-agentforce-subagents`
   skill; merge or re-scope any that share a job before generating.
3. **Choose Action Types**: Deterministic Flow/Apex actions for lookups, calculations, and
   transactions; prompt-template actions for generative work (per `sf-agentforce-actions`).
4. **Scaffold the Bundle** (default `force-app/main/default/`):
   - `aiAuthoringBundles/<agent>/<agent>.agent` — Agent Script: system + reasoning instructions,
     per-subagent scope and "do not" boundary, variables/conditionals, action references.
   - `genAiPlugins/` — one subagent per job with classification instructions.
   - `genAiFunctions/` — actions with precise "when to use" and described inputs/outputs; Apex
     `with sharing`, `WITH USER_MODE`, bind variables, returning only needed fields (no raw PII).
   - `genAiPromptTemplates/` — grounding templates with delimited context and citations.
   - `aiEvaluationDefinitions/` — starter eval (happy-path, out-of-scope, injection utterances).
5. **Report**: List files created and the DX lifecycle steps left (`sf project deploy start`,
   `sf agent publish authoring-bundle`, `sf agent activate`, `sf agent test run`).

## When to Use

Use `/sf-agentforce-build` when:
- Creating a new Agentforce agent from a description
- Adding a subagent + its action(s) to an existing agent
- Scaffolding an agent plus its grounding and a starter eval in one pass

## Related

- Agent: `sf-agentforce-builder`
- Review: `/sf-agentforce-review` (run after building)
- Refresh: `/sf-agentforce-refresh` (re-sync skills with current Salesforce docs)
- Rules: `rules/sf-agentforce/security.md`, `rules/sf-agentforce/instructions.md`, `rules/sf-agentforce/testing.md`, `rules/sf-agentforce/naming-conventions.md`
- Skills: `sf-agentforce-subagents`, `sf-agentforce-actions`, `sf-agentforce-grounding`, `sf-agentforce-testing`
