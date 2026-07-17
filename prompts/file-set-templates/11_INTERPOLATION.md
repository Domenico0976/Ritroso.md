# Template: 11_INTERPOLATION.md

> Answers: HOW does everything connect?
> Inference loop: receives question from 03_NEXT_STEPS, asks 12_ASKED.

---

```markdown
# 11_INTERPOLATION.md — {project_name}
{standard_header}

## CONNECTION MAP
<!-- How modules/elements/agents connect to each other -->
<!-- Format: A → B (connection_type) [direction] -->
{connection_map}

## CRITICAL DEPENDENCY
<!-- The single most critical dependency: if this breaks, what cascades? -->
<!-- [INF] if derived from inference loop answer to 03_NEXT_STEPS -->
{critical_dependency}

## DATA FLOWS
<!-- How data moves: input → transform → output -->
{data_flows}

## EXTERNAL INTEGRATIONS
<!-- Third-party APIs, services, webhooks, SDKs -->
{external_integrations}

## EVENTS / TRIGGERS
<!-- What triggers what: user action → system reaction -->
{events_triggers}

## SIMPLIFIED GRAPH
<!-- Text version of the project knowledge graph -->
{simplified_graph}

## INFERENCE QUESTION → 12_ASKED
<!-- Pre-generation: asked to 12_ASKED before this file was written -->
> "After this entire inference chain, what is the most important question
>  that still has no answer and would most change the design if answered?"
>
> Answer received: {inference_answer_from_12}

## INFERENCE ANSWER ← 03_NEXT_STEPS
<!-- Answer provided to 03_NEXT_STEPS during inference loop -->
> Question received: "What is the most critical dependency in the system?
>  If this dependency breaks, what cascades?"
> Answer given: {inference_answer_to_03}

## DEPENDENCIES
→ see [[04_ELEMENTS]] for connected elements
→ see [[09_AGENTS]] for agent interaction flows
→ see [[10_ERROR]] for failure modes in connections
→ see [[05_COMPONENTS]] for component interfaces
```
