# Template: 01_GOAL.md

> Answers: WHY does this project exist?
> First file generated. Sets the north star for all other files.
> Inference loop: asks 02_PRODUCT the alignment question before writing.

---

```markdown
---
name: "01_GOAL.md"
description: "Use this file to reason about the core objective of the project in its real operating context. Interpret all goals through the lens of user value, declared constraints, inferred limits, and what must explicitly remain out of scope. If ambition conflicts with feasibility, prioritize the goal that still survives technical, budget, time, and anti-pattern boundaries described across the linked files."
---

# 01_GOAL.md — {project_name}
{standard_header}

## CORE OBJECTIVE
<!-- The single sentence that explains why this project exists -->
<!-- [INF] if derived from inference loop -->
{core_objective}

## PROBLEM BEING SOLVED
<!-- What specific pain, gap or opportunity triggers this project -->
{problem_statement}

## SUCCESS CRITERIA
<!-- How do we know the project succeeded? Measurable where possible -->
{success_criteria}

## NON-GOALS
<!-- What this project explicitly does NOT aim to solve -->
{non_goals}

## STAKEHOLDERS
<!-- Who cares about this succeeding? Who loses if it fails? -->
{stakeholders}

## INFERENCE QUESTION → 02_PRODUCT
<!-- Pre-generation: asked to 02_PRODUCT before this file was written -->
> "Does the product as you imagine it actually solve this goal?
>  What is the most likely misalignment between goal and product?"
>
> Answer received: {inference_answer_from_02}

## DEPENDENCIES
→ see [[02_PRODUCT]] for what is built to achieve this goal
→ see [[08_LIMITS]] for constraints that bound this goal
→ see [[12_ASKED]] for unresolved questions about the goal itself
```
