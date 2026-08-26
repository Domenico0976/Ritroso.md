# Template: 02_PRODUCT.md

> Answers: WHAT does it do in detail?
> Inference loop: receives question from 01_GOAL, asks 04_ELEMENTS.

---

```markdown
---
name: "02_PRODUCT.md"
description: "Use this file to interpret what the product must actually do to satisfy the project goal without drifting into generic scope expansion. Read it as the executable translation of the goal under current domain constraints, inferred risks, and implementation boundaries. Prefer feature decisions that preserve goal-product alignment, avoid unnecessary complexity, and remain defensible against the limits, budget, and error scenarios documented elsewhere in the set."
---

# 02_PRODUCT.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW.
     Do not skip. This reasoning produces the content for every section. -->

**Step 1 — Translate the goal into product behavior.**
For each success criterion in [[01_GOAL]], name the specific product action that fulfills it.
Format: "To achieve [criterion], the product must [action]."
If any criterion has no corresponding action → it is unimplemented. Flag as [OPEN-INF-N].

**Step 2 — Find the minimum functional core.**
If the entire product were reduced to one screen and one action, what would they be?
This is your north star for SCOPE BOUNDARIES. Every feature that does not support this core is a candidate for NON-GOALS.

**Step 3 — Identify the highest-friction user flow.**
Which user flow involves the most steps, the most decisions, or the most dependencies on external systems?
This flow is the most likely source of drop-off, errors, and user frustration. It must appear explicitly in USER FLOWS with a note.

**Step 4 — Check for scope creep attractors.**
List 3 features that would be tempting to add but are not in [[01_GOAL]].
For each: explain in one sentence why it must not be in scope now.
These go into SCOPE BOUNDARIES as explicit exclusions.

**Step 5 — Validate product–goal alignment.**
Does the product as described here solve the CORE OBJECTIVE in [[01_GOAL]]?
Answer: YES / PARTIAL / NO + one sentence reason.
If PARTIAL or NO → name the gap and add it to BLOCKING QUESTIONS.

<!-- If any step cannot be completed → log as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send these queries before writing sections. Wait for answers. -->

**Query → context-gap agent:**
> "What product information is missing that would prevent a developer from starting implementation today?
>  List only critical gaps — not nice-to-haves."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a product that fails to achieve its goal while appearing complete look like?
>  Name the two most common patterns of product–goal misalignment for this type of product."
> Answer: {subagent_negative_verification_answer}

**Query → 04_ELEMENTS (pre-inference):**
> "What are the minimum elements required for this product to function at all?
>  What element, if missing, makes the product non-functional?"
> Answer: {subagent_elements_answer}

**Query → 01_GOAL (alignment check):**
> "Looking at the product as described, does it solve the goal?
>  Name the most likely point of misalignment."
> Answer: {subagent_goal_alignment_answer}

<!-- All answers inject into the sections below. Reference them inline. -->

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. This project-specific. No generic answers. -->

1. **Can a developer read CORE FEATURES and know exactly what to build?** If not, rewrite until yes.
2. **Do the USER FLOWS cover the error path, not just the happy path?** Every flow must have a failure branch.
3. **Is PRODUCT OVERVIEW one paragraph?** If longer, cut. If you cannot cut, the product is not well defined.
4. **Does each MODULE have a clear owner (human or agent)?** If not → add to [[09_AGENTS]] as [OPEN] slot.
5. **Is there a feature in CORE FEATURES that depends on an external service not listed in [[11_INTERPOLATION]]?** If yes → add it there now.

<!-- Flag unresolved answers as [OPEN-INF-N]. -->

---

## PRODUCT OVERVIEW
<!-- High-level description of what the product does — one paragraph max -->
<!-- Must reflect MODEL REASONING Step 2 minimum functional core -->
{product_overview}

## CORE FEATURES
<!-- Primary capabilities — what the user can actually do -->
<!-- Each feature must trace to a success criterion in [[01_GOAL]] -->
<!-- Format: Feature name · What the user does · What the system does · Output -->
{core_features}

## MODULES / SECTIONS
<!-- Internal decomposition of the product -->
<!-- Each module must have a named owner (human or agent) — see [[09_AGENTS]] -->
{modules}

## USER FLOWS
<!-- Main interaction paths: who does what, in what order -->
<!-- REQUIRED: include at least one failure branch per flow —
     Format: Step 1 → Step 2 → [SUCCESS path] / [FAILURE path → recovery action] -->
<!-- Highest-friction flow from MODEL REASONING Step 3 must be present with a [HIGH-FRICTION] tag -->
{user_flows}

## SCOPE BOUNDARIES
<!-- What is in scope vs explicitly out of scope -->
<!-- Must include the 3 scope-creep attractors identified in MODEL REASONING Step 4 -->
<!-- Format:
     IN SCOPE: {list}
     OUT OF SCOPE: {list — each item with one-sentence reason} -->
{scope_boundaries}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 5 returned PARTIAL or NO,
     or if SUB-AGENT QUERIES returned a critical gap.
     Do not ask about things that can be inferred.
     Stop generation and wait for answers before continuing. -->
{blocking_questions_to_user}

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
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[01_GOAL]] for the reason this product exists
→ see [[04_ELEMENTS]] for system elements that compose the product
→ see [[05_COMPONENTS]] for technical implementation
→ see [[06_PRICE]] for user-facing pricing
<!-- Add here: any external service dependency from SELF-INTERROGATION question 5 -->
```
