# Template: 10_ERROR.md

> Answers: WHAT can go wrong?
> Inference loop: receives question from 05_COMPONENTS, asks 08_LIMITS.

---

```markdown
---
name: "10_ERROR.md"
description: "Use this file to reason about how the project can fail in practice, not just in theory. Interpret errors through the most fragile components, likely cascades, silent failures, operational blind spots, and the limits required to contain them. Prefer realistic failure modeling over optimistic assumptions, and treat hidden breakpoints or unowned recovery paths as critical risks that must shape the rest of the design."
---

# 10_ERROR.md — {project_name}
{standard_header}

## FAILURE MODE MAP
<!-- All known failure modes with probability and impact -->
<!-- Format: Failure · Trigger · Probability (H/M/L) · Impact (H/M/L) · Recovery -->
{failure_mode_map}

## SINGLE POINTS OF FAILURE
<!-- Components or agents whose failure breaks the entire system -->
<!-- [INF] if identified during inference loop with 05_COMPONENTS -->
{single_points_of_failure}

## CASCADING FAILURES
<!-- If X fails, what else fails as a consequence? -->
{cascading_failures}

## RECOVERY PROTOCOLS
<!-- How to recover from each critical failure mode -->
{recovery_protocols}

## SILENT FAILURES
<!-- Failures that do not generate errors but corrupt outputs -->
{silent_failures}

## INFERENCE QUESTION → 08_LIMITS
<!-- Pre-generation: asked to 08_LIMITS before this file was written -->
> "Given these failure modes, what must be explicitly prohibited
>  or constrained to prevent systemic failure?"
>
> Answer received: {inference_answer_from_08}

## INFERENCE ANSWER ← 05_COMPONENTS
<!-- Answer provided to 05_COMPONENTS during inference loop -->
> Question received: "Which component is the most likely single point of failure?
>  What breaks first when this component fails?"
> Answer given: {inference_answer_to_05}

## DEPENDENCIES
→ see [[05_COMPONENTS]] for the components that can fail
→ see [[08_LIMITS]] for limits derived from these failure modes
→ see [[09_AGENTS]] for who handles recovery
→ see [[12_ASKED]] for unresolved failure scenarios
```
