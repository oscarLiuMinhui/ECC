---
description: Comprehensive Salesforce Agentforce review across Agent Script (*.agent / AiAuthoringBundle) and runtime metadata (GenAiPlannerBundle, GenAiPlugin subagents, GenAiFunction actions, prompt templates, AiEvaluationDefinition) — subagent routing, prompt-injection/scope guardrails, action sharing/CRUD-FLS, PII-into-LLM exposure, grounding, and eval coverage. Invokes the sf-agentforce-reviewer agent.
---

# Agentforce Review

This command invokes the **sf-agentforce-reviewer** agent for a comprehensive review of an
Agentforce agent across its design-time Agent Script and runtime GenAi metadata.

## What This Command Does

1. **Identify Agentforce Changes**: Find modified metadata via `git diff` — Agent Script
   `aiAuthoringBundles/<agent>/**/*.agent`, plus `genAiPlannerBundles/`, `genAiPlugins/`
   (subagents), `genAiFunctions/` (actions), `genAiPromptTemplates/`, `aiEvaluationDefinitions/`,
   and any Apex an action invokes.
2. **Routing Correctness**: Detect overlapping subagent scope (misrouting by the Atlas reasoning
   engine), vague action "when to use" instructions, undescribed inputs/outputs, and
   deterministic work left to free-form reasoning.
3. **Security & Guardrail Scan**: PII/secrets returned into the LLM context, Apex actions
   `without sharing` or skipping CRUD-FLS/`WITH USER_MODE`, SOQL injection from action inputs,
   and subagents with no explicit "do not" / scope boundary against prompt injection.
4. **Grounding Review**: Ungrounded answers, prompt templates without citations or with
   over-broad record context merged undelimited into the prompt.
5. **Eval Coverage**: Subagents/actions with no `AiEvaluationDefinition` case, and the absence of
   out-of-scope, injection, and context-variable tests before activation.
6. **Generate Report**: Categorize issues by severity (CRITICAL / HIGH / MEDIUM).

## When to Use

Use `/sf-agentforce-review` when:
- After building or modifying an agent, subagent, action, or eval
- Before publishing/activating an agent (`sf agent publish` / `sf agent activate`)
- Reviewing pull requests that contain Agentforce metadata
- Hardening an agent exposed to external or guest users

## Review Categories

### CRITICAL (Must Fix)
- PII/secrets exposed to the LLM; Apex action `without sharing` or no CRUD-FLS; SOQL injection
- Subagent with no scope/"do not" guardrail (prompt injection / data exfiltration)
- Overlapping subagent scope causing non-deterministic misrouting; vague action usage

### HIGH (Should Fix)
- Ungrounded answers; prompt template without citations / over-broad context
- No `AiEvaluationDefinition` coverage; no adversarial or context-variable tests

### MEDIUM (Consider)
- Weak system/reasoning instructions; no fallback when no subagent matches
- Unclear agent/subagent/action naming; missing descriptions

## Related

- Agent: `sf-agentforce-reviewer`
- Build: `/sf-agentforce-build` (scaffold before reviewing)
- Refresh: `/sf-agentforce-refresh` (re-sync skills with current Salesforce docs)
- Rules: `rules/sf-agentforce/security.md`, `rules/sf-agentforce/instructions.md`, `rules/sf-agentforce/testing.md`, `rules/sf-agentforce/naming-conventions.md`
- Skills: `sf-agentforce-subagents`, `sf-agentforce-actions`, `sf-agentforce-grounding`, `sf-agentforce-testing`
