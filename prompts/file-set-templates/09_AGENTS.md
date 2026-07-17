# Template: 09_AGENTS.md

> Answers: WHO does what (AI + humans)?
> Inference loop: receives question from 08_LIMITS, asks 07_BUDGET.

---

```markdown
# 09_AGENTS.md — {project_name}
{standard_header}

## AGENT MAP
<!-- All actors in the system: human roles + AI agents -->
<!-- Format: Agent · Type (human/AI) · Responsibility · Scope -->
{agent_map}

## AGENT RESPONSIBILITIES
<!-- What each agent owns, decides, executes -->
{agent_responsibilities}

## HANDOFF PROTOCOL
<!-- How tasks flow between agents: triggers, formats, acceptance criteria -->
{handoff_protocol}

## AGENT LIMITS
<!-- What each agent cannot or must not do -->
<!-- [INF] if derived from inference loop answer to 08_LIMITS -->
{agent_limits}

## ESCALATION PATHS
<!-- When an agent fails or is blocked: who takes over? -->
{escalation_paths}

## INFERENCE QUESTION → 07_BUDGET
<!-- Pre-generation: asked to 07_BUDGET before this file was written -->
> "What is the real cost of operating these agents at the defined limits?
>  Is there a scenario where the budget is exceeded by normal operations?"
>
> Answer received: {inference_answer_from_07}

## INFERENCE ANSWER ← 08_LIMITS
<!-- Answer provided to 08_LIMITS during inference loop -->
> Question received: "Who is responsible for monitoring these limits and responding to failures?
>  Is there an agent capable of doing this with current resources?"
> Answer given: {inference_answer_to_08}

## DEPENDENCIES
→ see [[08_LIMITS]] for what constrains agent behavior
→ see [[07_BUDGET]] for agent operation costs
→ see [[11_INTERPOLATION]] for agent interaction flows
→ see [[10_ERROR]] for agent failure modes
```
