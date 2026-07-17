# Template: 03_NEXT_STEPS.md

> Answers: WHAT TO DO NOW — ordered by priority.
> Inference loop: receives question from 06_PRICE, asks 11_INTERPOLATION.

---

```markdown
# 03_NEXT_STEPS.md — {project_name}
{standard_header}

## P1 — DO NOW (highest leverage, unblocks everything else)
<!-- Max 3 items. If there are more, you have not prioritized enough -->
{p1_actions}

## P2 — DO NEXT (important but not blocking)
{p2_actions}

## P3 — DO LATER (valuable but deferrable)
{p3_actions}

## BLOCKED BY
<!-- What must be resolved before any P1 action can start -->
{blockers}

## DECISION REQUIRED
<!-- Choices that must be made by a human, not the system -->
{decisions_required}

## INFERENCE QUESTION → 11_INTERPOLATION
<!-- Pre-generation: asked to 11_INTERPOLATION before this file was written -->
> "What is the most critical dependency in the system?
>  If this dependency breaks, what cascades?"
>
> Answer received: {inference_answer_from_11}

## INFERENCE ANSWER ← 06_PRICE
<!-- Answer provided to 06_PRICE during inference loop -->
> Question received: "What is the single action that, if done now, reduces the most uncertainty?"
> Answer given: {inference_answer_to_06}

## DEPENDENCIES
→ see [[01_GOAL]] for what these steps lead toward
→ see [[08_LIMITS]] for constraints on these actions
→ see [[09_AGENTS]] for who executes these steps
→ see [[12_ASKED]] for open questions that block prioritization
```
