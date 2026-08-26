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

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Map one component per element.**
For each element in [[04_ELEMENTS]] ELEMENT MAP: name the component that implements it.
If one component implements multiple elements → it is a SHARED COMPONENT. Flag it.
If an element has no component → it is unimplemented. Flag as [OPEN-INF-N].

**Step 2 — Name the architecture pattern before naming components.**
Choose: monolith / layered / event-driven / microservice / serverless / hybrid.
Justify in one sentence tied to a specific constraint (budget, team size, latency, scale).
The architecture choice constrains all component decisions below. If it is wrong → everything below is wrong.

**Step 3 — Find the single point of failure.**
Which component, if it fails, breaks the largest number of other components?
Trace the cascade: component X fails → Y cannot run → Z has no data → user sees [impact].
This component is the SPOF. It must have a mitigation plan, even if minimal.

**Step 4 — Validate the tech stack against the declared constraints.**
For each technology chosen: does it fit within the budget in [[07_BUDGET]]? Does it respect the limits in [[08_LIMITS]]?
If a technology requires infrastructure the budget cannot support → flag it and propose a lower-cost alternative.

**Step 5 — Check interface completeness.**
For each pair of components that must communicate: is the interface defined?
A component that produces output but has no defined consumer → orphaned output. Flag it.
A component that needs input but has no defined producer → blocked component. Flag it.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What technical implementation details are implied by the product description
>  but not yet named as components? List only gaps that would block a developer from starting."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a component architecture that looks complete but has a hidden single point of failure look like?
>  Name the two most common architectural mistakes for systems of this type and stack."
> Answer: {subagent_negative_verification_answer}

**Query → 10_ERROR (pre-inference):**
> "Given this component architecture, which component is the most likely single point of failure?
>  What breaks first when it fails, and what cascades?"
> Answer: {subagent_error_answer}

**Query → 07_BUDGET (cost check):**
> "Is the proposed tech stack sustainable within the declared budget?
>  Name any component whose operating cost at normal load would exceed its budget allocation."
> Answer: {subagent_budget_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every element in [[04_ELEMENTS]] have a named component?** If not → unimplemented element. Flag [OPEN-INF-N].
2. **Is the architecture pattern declared and justified?** If not → no architecture means no design. Write it first.
3. **Is the SPOF identified and does it have a mitigation plan?** MITIGATION STATUS: NONE is acceptable only if explicitly justified.
4. **Does the tech stack use real named technologies** (e.g. "Supabase" not "a database", "Next.js" not "a framework")? If generic terms appear → replace them.
5. **Is there an orphaned component** — one that produces output no other component consumes, or consumes input no other component produces? If yes → flag it as potentially unnecessary or missing a connection in [[11_INTERPOLATION]].

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## COMPONENT ARCHITECTURE
<!-- Architecture pattern (name it: monolith / layered / event-driven / microservice / serverless) -->
<!-- Rationale: one sentence tied to a specific constraint from MODEL REASONING Step 2 -->
<!-- Anti-pattern avoided: name the rejected architecture and the constraint that ruled it out -->
{architecture_overview}

## COMPONENT LIST
<!-- REQUIRED FORMAT — one row per component:
     | Component name | Type (UI/API/Service/Store/Infra/AI) | Responsibility (one sentence) | Shared? (YES — N elements / NO) | Stack item (real name) |
     Every element from [[04_ELEMENTS]] must have at least one corresponding component. -->
{component_list}

## SHARED COMPONENTS
<!-- Components used by ≥50% of elements — highest-leverage and highest-risk -->
<!-- [INF] tag if identified by inference loop -->
<!-- Derived from MODEL REASONING Step 1 -->
<!-- Format:
     ### {Component name}
     Used by: [elements from [[04_ELEMENTS]]]
     Risk: what happens to all dependents if this fails
     Mitigation: YES/NO/PARTIAL + description -->
{shared_components}

## TECH STACK
<!-- REQUIRED FORMAT:
     | Layer | Technology (real name) | Version/Tier | Justification (constraint or element it serves) |
     Validated against [[07_BUDGET]] and [[08_LIMITS]] in MODEL REASONING Step 4. -->
{tech_stack}

## INTERFACES
<!-- How components communicate — one entry per connection:
     Source → Target · Protocol · Payload shape (brief) · Failure behavior -->
<!-- Orphaned outputs/blocked inputs from MODEL REASONING Step 5 flagged here -->
{interfaces}

## SINGLE POINT OF FAILURE
<!-- MANDATORY — must not be empty or N/A unless explicitly justified -->
<!-- Derived from MODEL REASONING Step 3 -->
<!-- Format:
     Component: {name}
     Failure mode: {what breaks and why — specific to this stack}
     Cascade: {A fails → B blocked → C cannot complete — at least 2 levels}
     Detection: {how observable before users are affected}
     Mitigation status: NONE | PARTIAL ({what exists}) | COVERED ({mechanism}) -->
{spof_section}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 4 found a technology the budget cannot support,
     or if SELF-INTERROGATION question 2 found no justifiable architecture pattern.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

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
<!-- Add here: orphaned components from MODEL REASONING Step 5 → [[11_INTERPOLATION]] -->
```
