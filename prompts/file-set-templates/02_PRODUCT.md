# Template: 02_PRODUCT.md

> Answers: WHAT does it do in detail?
> Inference loop: receives question from 01_GOAL, asks 04_ELEMENTS.

---

```markdown
# 02_PRODUCT.md — {project_name}
{standard_header}

## PRODUCT OVERVIEW
<!-- High-level description of what the product does -->
{product_overview}

## CORE FEATURES
<!-- Primary capabilities — what the user can actually do -->
{core_features}

## MODULES / SECTIONS
<!-- Internal decomposition of the product -->
{modules}

## USER FLOWS
<!-- Main interaction paths: who does what, in what order -->
{user_flows}

## SCOPE BOUNDARIES
<!-- What is in scope vs explicitly out of scope -->
{scope_boundaries}

## INFERENCE QUESTION → 04_ELEMENTS
<!-- Pre-generation: asked to 04_ELEMENTS before this file was written -->
> "What are the minimum elements required for this product to function at all?
>  What element, if missing, makes the product non-functional?"
>
> Answer received: {inference_answer_from_04}

## INFERENCE ANSWER ← 01_GOAL
<!-- Answer provided to 01_GOAL during inference loop -->
> Question received: "Does the product actually solve the goal? Most likely misalignment?"
> Answer given: {inference_answer_to_01}

## DEPENDENCIES
→ see [[01_GOAL]] for the reason this product exists
→ see [[04_ELEMENTS]] for system elements that compose the product
→ see [[05_COMPONENTS]] for technical implementation
→ see [[06_PRICE]] for user-facing pricing
```
