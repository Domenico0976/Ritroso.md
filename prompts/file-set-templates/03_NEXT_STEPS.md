# Template: 03_NEXT_STEPS.md

> Answers: WHAT TO DO NOW — ordered by priority.
> Inference loop: receives question from 06_PRICE, asks 11_INTERPOLATION.

---

```markdown
---
name: "03_NEXT_STEPS.md"
description: "Use this file to decide the most leverage-rich actions in the current project state. Interpret prioritization through uncertainty reduction, dependency pressure, inferred blockers, and the limits already known from budget, scope, architecture, or user context. Prefer actions that validate viability quickly, reduce risk early, and avoid busywork or speculative execution unsupported by the rest of the file set."
---

# 03_NEXT_STEPS.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — List every unresolved [OPEN-INF-N] across all files.**
Go through [[12_ASKED]] and every [OPEN-INF-N] logged during the inference loop.
For each open item: would resolving it change what is built? YES/MAYBE/NO.
YES items must map to a P1 or P2 action. NO items are deferred to P3 or dropped.

**Step 2 — Identify the single highest-uncertainty dimension.**
What is the one thing the team does not know that, if wrong, would cause the most rework?
This becomes the first P1 action: validate it before building anything that depends on it.

**Step 3 — Map dependency pressure.**
Which P2/P3 actions are blocked by a P1 action?
Format as: P1-action → unblocks → [P2-a, P2-b, P3-c].
If a P2 action has no dependency on a P1, ask: why is it not P1?

**Step 4 — Assign each action to an agent.**
Every action must have a named owner from [[09_AGENTS]].
If an action has no owner → it is an [OPEN] agent slot. Add to [[12_ASKED]] and flag BLOCKED BY.

**Step 5 — Time-box P1 actions.**
Each P1 action must have a concrete time estimate: hours or days for one person.
If you cannot estimate it → the action is not specific enough. Break it down.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. Wait for answers. -->

**Query → context-gap agent:**
> "What context is missing that would change the prioritization of these actions?
>  List only gaps that would promote a P2 to P1 or demote a P1 to blocked."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a next-steps plan that looks complete but executes in the wrong order look like?
>  Name the two most common prioritization mistakes for projects of this type."
> Answer: {subagent_negative_verification_answer}

**Query → 11_INTERPOLATION (dependency check):**
> "What is the most critical dependency in the system?
>  If this dependency breaks, what cascades — and does any P1 action address it?"
> Answer: {subagent_interpolation_answer}

**Query → 12_ASKED (open items):**
> "Which open questions from the full file set, if answered now, would most change
>  the action list? Rank top 3 by impact on next steps."
> Answer: {subagent_asked_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Are there more than 3 P1 actions?** If yes → you have not prioritized. Demote the weakest to P2.
2. **Does every P1 action reduce a specific uncertainty identified in MODEL REASONING Step 2?** If not → it is P2 at best.
3. **Is every P1 action owned by a named agent from [[09_AGENTS]]?** If not → flag BLOCKED BY.
4. **Does the BLOCKED BY section explain WHY, not just list what?** A blocker without a reason is not actionable.
5. **Are all DECISION REQUIRED items things only a human can decide?** If a decision can be inferred from available data → make the decision and document it as an assumption in [[12_ASKED]].

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## P1 — DO NOW (highest leverage, unblocks everything else)
<!-- Max 3 items. If more exist, you have not prioritized enough.
     Format: Action · Owner (from [[09_AGENTS]]) · Time estimate · Uncertainty it resolves -->
{p1_actions}

## P2 — DO NEXT (important but not blocking)
<!-- Format: Action · Owner · Depends on (P1-action name or NONE) -->
{p2_actions}

## P3 — DO LATER (valuable but deferrable)
<!-- Format: Action · Condition that would promote it to P2 -->
{p3_actions}

## BLOCKED BY
<!-- What must be resolved before any P1 action can start.
     Format: P1-action blocked · Blocked by (specific decision, open question, or missing input) · Who can unblock it -->
{blockers}

## DECISION REQUIRED
<!-- Choices that must be made by a human, not the system.
     Format: Decision · Impact on action plan · Deadline (or NONE) -->
<!-- Must include SELF-INTERROGATION question 5 results -->
{decisions_required}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 4 found an unowned P1 action,
     or if BLOCKED BY has no known unblocking path.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 11_INTERPOLATION
<!-- Pre-generation: asked to 11_INTERPOLATION before this file was written -->
> "What is the most critical dependency in the system?
>  If this dependency breaks, what cascades?"
>
> Answer received: {inference_answer_from_11}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 06_PRICE
<!-- Answer provided to 06_PRICE during inference loop -->
> Question received: "What is the single action that, if done now, reduces the most uncertainty?"
> Answer given: {inference_answer_to_06}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[01_GOAL]] for what these steps lead toward
→ see [[08_LIMITS]] for constraints on these actions
→ see [[09_AGENTS]] for who executes these steps
→ see [[12_ASKED]] for open questions that block prioritization
<!-- Add here: dependency pressure map from MODEL REASONING Step 3 -->
```
