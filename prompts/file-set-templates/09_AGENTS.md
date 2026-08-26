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

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW.
     This reasoning is mandatory — it produces the agent map and responsibility structure. -->

**Step 1 — List every decision point in the system.**
For each decision point identified in [[02_PRODUCT]] USER FLOWS and [[11_INTERPOLATION]] EVENTS/TRIGGERS:
- Name the decision
- Classify it: automated (no human needed) / assisted (human confirms) / human-only (AI must not decide)
If a decision point has no owner → it is an [OPEN] agent slot. Flag immediately.

**Step 2 — Identify which agents the Ritroso.md skill already provides.**
List the sub-agents available in this skill invocation (context-gap, negative-verification, inference-loop, etc.).
For each: does their capability match a decision point identified in Step 1?
Map explicitly: agent → decision point it covers.

**Step 3 — Find the accountability gap.**
After mapping, which decision points have no agent assigned?
These are your highest-priority [OPEN] slots. They must appear in 12_ASKED as blocking questions.
An unowned decision that can cause a failure cascade (see [[10_ERROR]]) is a CRITICAL gap.

**Step 4 — Stress-test each agent against 08_LIMITS.**
For each agent you plan to define: can it operate within the declared limits (context window, budget, latency, autonomy scope)?
If an agent requires more than the limits allow → it must be split, downgraded to assisted, or replaced by a human role.

**Step 5 — Define the minimum agent set.**
What is the smallest number of agents that covers all owned decision points?
More agents = more coordination cost. If two agents do similar things → merge them.
Document why each agent in the final map is necessary and not replaceable.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send these queries to skill sub-agents before writing sections. -->

**Query → context-gap agent:**
> "What agent roles are implied by the product description but not yet named?
>  List only roles where the absence of an owner would cause a silent failure."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a well-defined agent map that still fails look like?
>  Name the two most common agent design failures for systems of this type
>  (e.g., overlapping responsibilities, missing escalation path, budget-blind autonomy)."
> Answer: {subagent_negative_verification_answer}

**Query → 08_LIMITS (constraint check):**
> "Which of the declared limits most constrains what agents can do autonomously?
>  Is there a limit that would force a human-in-the-loop pattern you haven't planned for?"
> Answer: {subagent_limits_answer}

**Query → 07_BUDGET (cost check):**
> "What is the real cost of operating these agents at the defined limits?
>  Is there a scenario where normal operations exceed the declared budget?"
> Answer: {subagent_budget_answer}

**Query → inference-loop agent (self-check):**
> "After completing the inference loop, which agent responsibility was most frequently referenced
>  by other files but never explicitly assigned?"
> Answer: {subagent_inference_loop_answer}

<!-- All answers inject into sections below. Reference them explicitly. -->

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. Specific to this project. -->

1. **Does every decision point from MODEL REASONING Step 1 have a named owner?** If not → [OPEN] slot in AGENT MAP.
2. **Is there an agent that can make irreversible actions without human confirmation?** If yes → does 08_LIMITS explicitly constrain it? If not → add the constraint now.
3. **What happens if the primary AI agent is unavailable?** Is there a fallback human role or a graceful degradation path? If neither → ESCALATION PATHS is incomplete.
4. **Does any agent need access to data it is not explicitly given?** If yes → it is an implicit dependency. Add to [[11_INTERPOLATION]].
5. **Can the budget in [[07_BUDGET]] sustain all agents for 30 days of normal operation?** Answer YES/NO/UNKNOWN. If UNKNOWN → add to BLOCKING QUESTIONS.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## AGENT MAP
<!-- REQUIRED FORMAT — one row per agent:
     | Agent name (specific role) | Type (Human/AI-autonomous/AI-assisted/External) | Primary responsibility | Decision scope |
     Minimum: all agents identified in MODEL REASONING Step 1 must appear here.
     Agents from Ritroso.md skill sub-agents must be mapped to their decision points (from Step 2).
     If a limit in 08_LIMITS has no owner → flag that slot as [OPEN] -->
{agent_map}

## AGENT RESPONSIBILITIES
<!-- For each agent — expand the AGENT MAP row:
     ### {Agent name}
     Owns: {decisions/actions/outputs solely responsible for}
     Cannot: {what this agent must NOT do — tied to 08_LIMITS}
     Triggers: {what events cause this agent to act}
     Output format: {file, API call, message, decision log}
     Budget envelope: {cost per operation or per month — from 07_BUDGET}
     Sub-agent mapping: {if a Ritroso.md skill sub-agent, name the decision point it covers from MODEL REASONING Step 2} -->
{agent_responsibilities}

## HANDOFF PROTOCOL
<!-- How tasks flow between agents — each handoff must specify:
     From → To · Trigger condition · Required input format · Acceptance criteria · Rejection handling -->
<!-- No generic "agent A passes to agent B" — name the specific trigger and format -->
{handoff_protocol}

## AGENT LIMITS
<!-- What each agent cannot or must not do — derived from 08_LIMITS -->
<!-- [INF] tag required if derived from inference loop answer -->
<!-- Format:
     | Agent | Limit | Source (08_LIMITS section) | Consequence of violation |
     Unowned limits from 08_LIMITS with no agent here → [OPEN] in 12_ASKED.md -->
{agent_limits}

## ESCALATION PATHS
<!-- When an agent fails, is blocked, or exceeds its scope: who takes over?
     Must cover: agent failure, budget exceeded, ambiguous input, conflicting instructions -->
<!-- Format:
     {Agent} → blocked/failed → escalates to {Agent or human} → via {channel} → within {time} -->
<!-- SELF-INTERROGATION question 3 answer must be reflected here -->
{escalation_paths}

## CAPABILITY ASSESSMENT
<!-- MANDATORY: can each agent actually do what is asked with the declared resources? -->
<!-- [INF] if derived from inference loop answer -->
<!-- Derived from MODEL REASONING Step 4 stress-test -->
<!-- Format:
     | Agent | Task | Feasible? (YES/PARTIAL/NO) | Constraint | Mitigation -->
{capability_assessment}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 3 found a CRITICAL unowned decision,
     or if SELF-INTERROGATION question 5 returned UNKNOWN.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

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
<!-- Add here: any implicit data dependency from SELF-INTERROGATION question 4 -->
```
