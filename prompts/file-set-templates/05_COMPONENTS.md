# Template: 05_COMPONENTS.md

> Answers: HOW is it built?
> Inference loop: receives question from 04_ELEMENTS, asks 10_ERROR.

---

```markdown
---
name: "05_COMPONENTS.md"
description: "Use this file to interpret the technical construction of the project from a feasibility-first perspective. Read components as implementation commitments constrained by the inferred stack, operational limits, shared dependencies, and known failure surfaces. Prefer reusable and high-leverage components, reject architecture that adds complexity without supporting the goal, and keep an eye on which component becomes the most dangerous single point of failure."
---

# 05_COMPONENTS.md — {project_name}
{standard_header}

## COMPONENT ARCHITECTURE
<!-- Overall architecture pattern and rationale -->
{architecture_overview}

## COMPONENT LIST
<!-- All technical components with type and responsibility -->
<!-- Format: Component · Type · Responsibility · Shared? -->
{component_list}

## SHARED COMPONENTS
<!-- Components used by more than 50% of elements -- highest leverage -->
<!-- [INF] if identified by inference loop -->
{shared_components}

## TECH STACK
<!-- Technologies, frameworks, libraries, services -->
{tech_stack}

## INTERFACES
<!-- How components talk to each other: API, events, shared state -->
{interfaces}

## INFERENCE QUESTION → 10_ERROR
<!-- Pre-generation: asked to 10_ERROR before this file was written -->
> "Which component is the most likely single point of failure?
>  What breaks first when this component fails?"
>
> Answer received: {inference_answer_from_10}

## INFERENCE ANSWER ← 04_ELEMENTS
<!-- Answer provided to 04_ELEMENTS during inference loop -->
> Question received: "Which components are needed? Are any used by more than half the elements?"
> Answer given: {inference_answer_to_04}

## DEPENDENCIES
→ see [[04_ELEMENTS]] for the elements these components implement
→ see [[10_ERROR]] for failure modes of these components
→ see [[07_BUDGET]] for the cost of operating these components
→ see [[11_INTERPOLATION]] for how components interconnect
```
