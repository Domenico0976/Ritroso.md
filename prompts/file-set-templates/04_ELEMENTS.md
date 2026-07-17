# Template: 04_ELEMENTS.md

> Answers: WHAT does the system contain?
> Inference loop: receives question from 02_PRODUCT, asks 05_COMPONENTS.

---

```markdown
# 04_ELEMENTS.md — {project_name}
{standard_header}

## ELEMENT MAP
<!-- All entities in the system: what they are, what they do -->
<!-- Format: Element · Type · Description · Critical? -->
{element_map}

## MINIMUM VIABLE ELEMENTS
<!-- The elements without which the system cannot function at all -->
<!-- [INF] if derived from inference loop answer to 02_PRODUCT -->
{minimum_viable_elements}

## ELEMENT RELATIONSHIPS
<!-- How elements connect or depend on each other -->
<!-- Format: A ↔ B (relationship type) -->
{element_relationships}

## ELEMENT STATES
<!-- Lifecycle of key elements: created → active → archived/deleted -->
{element_states}

## INFERENCE QUESTION → 05_COMPONENTS
<!-- Pre-generation: asked to 05_COMPONENTS before this file was written -->
> "Which components are needed to implement these elements?
>  Are there components used by more than half of the elements?"
>
> Answer received: {inference_answer_from_05}

## INFERENCE ANSWER ← 02_PRODUCT
<!-- Answer provided to 02_PRODUCT during inference loop -->
> Question received: "What are the minimum elements required? What element, if missing, breaks the product?"
> Answer given: {inference_answer_to_02}

## DEPENDENCIES
→ see [[02_PRODUCT]] for what product these elements compose
→ see [[05_COMPONENTS]] for how these elements are implemented
→ see [[11_INTERPOLATION]] for the connection map between elements
```
