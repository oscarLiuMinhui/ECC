---
paths:
  - "**/aiAuthoringBundles/**"
  - "**/*.agent"
  - "**/genAiPlannerBundles/**"
  - "**/genAiPlugins/**"
  - "**/genAiFunctions/**"
  - "**/genAiPromptTemplates/**"
  - "**/aiEvaluationDefinitions/**"
---
# Agentforce Naming Conventions

> Naming and metadata conventions for Agentforce agents so a growing org's agents, subagents,
> actions, and evals stay discoverable and self-describing.

API names and labels are also read by the reasoning engine and by humans maintaining the agent,
so clear, consistent names reduce both misrouting and maintenance cost.

## Agents & subagents

- **Agent** API name: PascalCase, describing the agent's domain (e.g. `OrderSupportAgent`).
- **Subagent** (`GenAiPlugin`) API name: PascalCase naming the job, not the implementation
  (`OrderStatus`, `ReturnsAndRefunds`) — one job per name.
- Give every agent and subagent a **non-empty description** that states its scope.

## Actions

- **Action** (`GenAiFunction`) API name: verb-first PascalCase describing the capability
  (`GetOrderStatus`, `StartReturn`).
- **Inputs/outputs**: descriptive names with a description; avoid `input1`, `tmp`, `data`.

## Prompt templates & evals

- **Prompt template** name reflects what it grounds/produces (`OrderStatusAnswer`).
- **Eval** (`AiEvaluationDefinition`) name pairs with the agent/subagent it covers
  (`OrderSupportAgentEval`); test-case names describe the scenario.

## Consistency

- One casing/label convention across the org's agent library.
- Names match the job-to-be-done, so a reader can map utterance → subagent → action by name.
- Avoid encoding version or environment in the API name; use metadata/versioning for that.

## Review Checklist

- [ ] Agent/subagent/action API names are PascalCase and describe the job/capability
- [ ] One job per subagent name; verb-first action names
- [ ] Inputs/outputs descriptively named with descriptions
- [ ] Agents, subagents, and actions have non-empty descriptions
- [ ] Eval and test-case names map to what they cover
- [ ] Consistent convention across the agent library
