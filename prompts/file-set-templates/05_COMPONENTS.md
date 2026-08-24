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
<!-- Overall architecture pattern (name it: monolith / layered / event-driven / microservice / serverless) -->
<!-- Rationale: one sentence explaining WHY this pattern fits this project's constraints -->
<!-- Anti-pattern avoided: name the architecture that was rejected and why -->
{architecture_overview}

## COMPONENT LIST
<!-- REQUIRED FORMAT — one row per component, no exceptions:
     | Component name | Type (UI/API/Service/Store/Infra/AI) | Responsibility (one sentence, project-specific) | Shared? (YES — used by N elements / NO) | Stack item (real library/service name) |
     Component names must be specific (e.g. "UserSessionStore" not "a database") -->
{component_list}

## SHARED COMPONENTS
<!-- Components used by ≥50% of elements — these are the highest-leverage and highest-risk -->
<!-- [INF] tag required if identified by inference loop -->
<!-- For each shared component:
     - Name
     - Used by: [list of elements from 04_ELEMENTS]
     - Risk: what happens to all dependents if this fails
     - Mitigation: is there a fallback? YES/NO/PARTIAL -->
{shared_components}

## TECH STACK
<!-- REQUIRED FORMAT:
     | Layer | Technology | Version/Tier | Justification (tied to constraint or element) |
     No generic entries ("a database", "cloud provider") — name the real service -->
{tech_stack}

## INTERFACES
<!-- How components communicate — one entry per connection:
     Source → Target · Protocol (REST/event/function call/shared state) · Payload shape (brief) · Failure behavior -->
{interfaces}

## SINGLE POINT OF FAILURE
<!-- MANDATORY SECTION — must not be empty or N/A unless explicitly justified -->
<!-- Derived from inference loop answer to 10_ERROR — tag with [INF] -->
<!-- Format:
     Component: {name}
     Failure mode: {what breaks and why — specific to this stack}
     Cascade: {A fails → B is blocked → C cannot complete — at least 2 levels}
     Detection: {how is this observable before users are affected}
     Mitigation status: NONE | PARTIAL ({what exists}) | COVERED ({mechanism}) -->
{spof_section}

## INFERENCE QUESTION → 10_ERROR
<!-- Pre-generation: asked to 10_ERROR before this file was written -->
> "Which component is the most likely single point of failure?
>  What breaks first when this component fails?"
>
> Answer received: {inference_answer_from_10}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 04_ELEMENTS
<!-- Answer provided to 04_ELEMENTS during inference loop -->
> Question received: "Which components are needed? Are any used by more than half the elements?"
> Answer given: {inference_answer_to_04}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[04_ELEMENTS]] for the elements these components implement
→ see [[10_ERROR]] for failure modes of these components
→ see [[07_BUDGET]] for the cost of operating these components
→ see [[11_INTERPOLATION]] for how components interconnect
```
