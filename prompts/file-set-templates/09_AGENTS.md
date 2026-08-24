# Template: 09_AGENTS.md

> Answers: WHO does what (AI + humans)?
> Inference loop: receives question from 08_LIMITS, asks 07_BUDGET.

---

```markdown
---
name: "09_AGENTS.md"
description: "Use this file to interpret who owns decisions, execution, monitoring, and escalation across humans and AI agents. Read each role under the pressure of inferred limits, accountability boundaries, budget realism, and failure response requirements. Prefer clear responsibility mapping and constrained delegation; avoid agent definitions that assume unlimited context, unlimited autonomy, or budget the project cannot actually sustain."
---

# 09_AGENTS.md — {project_name}
{standard_header}

## AGENT MAP
<!-- REQUIRED FORMAT — one row per agent:
     | Agent name (specific role, not "admin" or "user") | Type (Human/AI-autonomous/AI-assisted/External) | Primary responsibility (one sentence, project-specific) | Decision scope (what they can decide alone vs. must escalate) |
     Minimum: all agents identified in the inference loop must appear here.
     If a limit in 08_LIMITS has no owner → flag that agent slot as [OPEN] -->
{agent_map}

## AGENT RESPONSIBILITIES
<!-- For each agent — expand the AGENT MAP row with:
     ### {Agent name}
     Owns: {list of decisions/actions/outputs this agent is solely responsible for}
     Cannot: {what this agent must NOT do — tied to 08_LIMITS}
     Triggers: {what events cause this agent to act}
     Output format: {what this agent produces — file, API call, message, decision log}
     Budget envelope: {cost per operation or per month — even rough — from 07_BUDGET} -->
{agent_responsibilities}

## HANDOFF PROTOCOL
<!-- How tasks flow between agents — each handoff must specify:
     From → To · Trigger condition · Required input format · Acceptance criteria · Rejection handling -->
<!-- No generic "agent A passes to agent B" — name the specific trigger and format -->
{handoff_protocol}

## AGENT LIMITS
<!-- What each agent cannot or must not do — derived from 08_LIMITS -->
<!-- [INF] tag required if derived from inference loop answer -->
<!-- REQUIRED: every agent must have at least one explicit limit.
     An agent with no limits is a design error. -->
<!-- Format:
     | Agent | Limit | Source (08_LIMITS section) | Consequence of violation |
     Unowned limits from 08_LIMITS that have no agent here → [OPEN] in 12_ASKED.md -->
{agent_limits}

## ESCALATION PATHS
<!-- When an agent fails, is blocked, or exceeds its scope: who takes over?
     Must cover: agent failure, budget exceeded, ambiguous input, conflicting instructions -->
<!-- Format:
     {Agent} → blocked/failed → escalates to {Agent or human} → via {channel/mechanism} → within {time} -->
{escalation_paths}

## CAPABILITY ASSESSMENT
<!-- MANDATORY: can each agent actually do what is asked with the declared resources? -->
<!-- [INF] if derived from inference loop answer to 08_LIMITS -->
<!-- Format:
     | Agent | Task | Feasible? (YES/PARTIAL/NO) | Constraint that limits it | Mitigation -->
{capability_assessment}

## INFERENCE QUESTION → 07_BUDGET
<!-- Pre-generation: asked to 07_BUDGET before this file was written -->
> "What is the real cost of operating these agents at the defined limits?
>  Is there a scenario where the budget is exceeded by normal operations?"
>
> Answer received: {inference_answer_from_07}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 08_LIMITS
<!-- Answer provided to 08_LIMITS during inference loop -->
> Question received: "Who is responsible for monitoring these limits and responding to failures?
>  Is there an agent capable of doing this with current resources?"
> Answer given: {inference_answer_to_08}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[08_LIMITS]] for what constrains agent behavior
→ see [[07_BUDGET]] for agent operation costs
→ see [[11_INTERPOLATION]] for agent interaction flows
→ see [[10_ERROR]] for agent failure modes
```
