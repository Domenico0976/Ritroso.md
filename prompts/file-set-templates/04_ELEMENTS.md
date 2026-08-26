# Template: 04_ELEMENTS.md

> Answers: WHAT does the system contain?
> Inference loop: receives question from 02_PRODUCT, asks 05_COMPONENTS.

---

```markdown
---
name: "04_ELEMENTS.md"
description: "Use this file to reason about the minimum and full set of elements required by the system in the current context. Interpret every element by its necessity, relationship to the product goal, and its exposure to inferred constraints such as missing resources, fragile dependencies, or scope compression. Favor element definitions that preserve system function with the lowest unjustified complexity."
---

# 04_ELEMENTS.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Derive elements from product features, not from intuition.**
For each feature in [[02_PRODUCT]] CORE FEATURES, name the system entity that feature requires to exist.
Format: Feature "X" requires element "Y" because [specific reason].
Do not invent elements that no feature requires.

**Step 2 — Separate WHAT from HOW.**
Elements are system entities (user, session, document, event, rule, report).
Components are their implementations (database, API, queue, cache).
If you find yourself writing a technology name here → it belongs in [[05_COMPONENTS]], not here.

**Step 3 — Find the single critical element.**
Which element, if removed, makes the product non-functional in its entirety?
This is the CRITICAL element. It must be tagged [CRITICAL] in ELEMENT MAP.
There should be exactly one — if you have more than one, the product has multiple independent cores.

**Step 4 — Map element relationships.**
For each pair of elements: do they need each other to function, or are they independent?
Relationships that are bidirectional (A needs B AND B needs A) are potential circular dependencies.
Flag bidirectional relationships for review in ELEMENT RELATIONSHIPS.

**Step 5 — Define element lifecycle.**
For each element: when is it created, when does it change state, when is it removed?
If an element has no clear end state → it accumulates indefinitely → potential data/cost risk. Flag it.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What system entities are implied by the product description but have not been named as elements?
>  List only entities whose absence would cause a silent functional gap."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does an element map that looks complete but hides a missing entity look like?
>  Name the two most common omission patterns in element modeling for this type of system."
> Answer: {subagent_negative_verification_answer}

**Query → 02_PRODUCT (source check):**
> "For each element in this file: which feature in 02_PRODUCT requires it?
>  If an element cannot be traced to a feature, should it be removed or is it infrastructure?"
> Answer: {subagent_product_trace_answer}

**Query → 05_COMPONENTS (pre-inference):**
> "Which components are needed to implement these elements?
>  Are there components that would serve more than half of the elements listed here?"
> Answer: {subagent_components_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every element trace to at least one feature in [[02_PRODUCT]]?** Elements with no feature trace are either infrastructure (label them) or scope creep (remove them).
2. **Is there exactly one [CRITICAL] element?** If zero → the product has no core. If more than one → the product has multiple cores and may need to be split.
3. **Are all elements things (nouns), not actions (verbs)?** If you wrote a verb → it is a user flow, not an element. Move it to [[02_PRODUCT]].
4. **Do any bidirectional relationships risk circular initialization?** If yes → flag in ELEMENT RELATIONSHIPS and add to [[12_ASKED]].
5. **Does any element have no defined end state?** If yes → it accumulates indefinitely. Flag as a potential cost/storage risk in [[07_BUDGET]].

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## ELEMENT MAP
<!-- All entities in the system: what they are, what they do -->
<!-- Format: Element · Type (data entity/actor/rule/state/event) · Description · Critical? -->
<!-- [CRITICAL] tag on the single most critical element from MODEL REASONING Step 3 -->
{element_map}

## MINIMUM VIABLE ELEMENTS
<!-- The elements without which the system cannot function at all -->
<!-- [INF] if derived from inference loop answer to 02_PRODUCT -->
<!-- Must reflect MODEL REASONING Step 3 — list the [CRITICAL] element first -->
{minimum_viable_elements}

## ELEMENT RELATIONSHIPS
<!-- How elements connect or depend on each other -->
<!-- Format: A ↔ B (bidirectional, note circular risk if present) | A → B (unidirectional) -->
<!-- Bidirectional relationships from MODEL REASONING Step 4 flagged here -->
{element_relationships}

## ELEMENT STATES
<!-- Lifecycle of key elements: created → active → archived/deleted -->
<!-- Elements with no end state from MODEL REASONING Step 5 flagged as [ACCUMULATES] -->
{element_states}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 3 found no single critical element,
     or if SELF-INTERROGATION question 4 found a circular dependency risk.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 05_COMPONENTS
<!-- Pre-generation: asked to 05_COMPONENTS before this file was written -->
> "Which components are needed to implement these elements?
>  Are there components used by more than half of the elements?"
>
> Answer received: {inference_answer_from_05}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 02_PRODUCT
<!-- Answer provided to 02_PRODUCT during inference loop -->
> Question received: "What are the minimum elements required? What element, if missing, breaks the product?"
> Answer given: {inference_answer_to_02}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[02_PRODUCT]] for what product these elements compose
→ see [[05_COMPONENTS]] for how these elements are implemented
→ see [[11_INTERPOLATION]] for the connection map between elements
<!-- Add here: any accumulation risk from MODEL REASONING Step 5 → [[07_BUDGET]] -->
<!-- Add here: any circular dependency from SELF-INTERROGATION question 4 → [[12_ASKED]] -->
```
