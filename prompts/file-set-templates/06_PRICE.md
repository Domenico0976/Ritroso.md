# Template: 06_PRICE.md

> Answers: HOW MUCH does it cost for the user?
> Inference loop: receives question from 07_BUDGET, asks 03_NEXT_STEPS.

---

```markdown
---
name: "06_PRICE.md"
description: "Use this file to reason about user-facing pricing in relation to real value, operating cost, and adoption risk. Interpret pricing as a strategic constraint, not just a number: it must survive inferred budget pressure, product maturity, target-user tolerance, and competitive alternatives. Prefer pricing that keeps the project sustainable without overpromising value the current scope, stack, or delivery capacity cannot support."
---

# 06_PRICE.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Establish the cost floor.**
From [[07_BUDGET]] SUSTAINABILITY THRESHOLD: what is the minimum monthly cost to keep the product running?
This is the absolute price floor: pricing below it means operating at a loss.
If this number is unknown → the budget file is incomplete → BLOCKING QUESTION.

**Step 2 — Estimate the value ceiling.**
What is the maximum a rational user in the target segment would pay for this product?
Base this on: alternatives available, time/money saved, unique value delivered.
If no comparable alternatives exist → use the closest proxy and note the uncertainty.

**Step 3 — Identify the pricing model that fits the product behavior.**
Matching rules:
- One-time use or episodic value → per-use / one-time payment
- Continuous value, recurring engagement → subscription
- Variable consumption (API, storage, seats) → usage-based
- Internal tool / no revenue model → mark as N/A with reason
If the product behavior does not match the chosen model → explain the mismatch explicitly.

**Step 4 — Stress-test the revenue scenarios.**
For each scenario (best / expected / minimum viable): at what number of paying users is the project sustainable?
If the minimum viable scenario requires an unrealistic user count given the product's current stage → the price is too low or the cost is too high. Flag it.

**Step 5 — Check pricing against [[08_LIMITS]] constraints.**
Are there legal, regulatory, or scope limits that constrain pricing options?
(e.g. cannot charge per user due to licensing, must offer a free tier due to competition, market requires annual contracts).
If yes → note as a hard constraint in PRICING MODEL.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What pricing context is missing that would change the recommended model or tier structure?
>  List only gaps that would shift the pricing strategy, not just refine it."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a pricing model that looks sustainable but fails in practice look like?
>  Name the two most common pricing mistakes for products at this stage and in this market."
> Answer: {subagent_negative_verification_answer}

**Query → 07_BUDGET (cost floor):**
> "What is the minimum monthly operating cost from 07_BUDGET?
>  At what price point and user count does revenue cover costs?"
> Answer: {subagent_budget_floor_answer}

**Query → 03_NEXT_STEPS (pre-inference):**
> "What is the single action that, if done now, most reduces uncertainty about
>  whether this pricing model is viable in the target market?"
> Answer: {subagent_next_steps_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Is the cost floor from [[07_BUDGET]] known and reflected in PRICING MODEL?** If not → price is set blindly.
2. **Does the pricing model match the product's consumption pattern** (MODEL REASONING Step 3)? A mismatch creates churn or undercharging.
3. **Does MINIMUM VIABLE scenario in REVENUE SCENARIOS require a realistic user count for this stage?** If it requires >1000 users to break even on a pre-launch product → the model needs revision.
4. **Is the COMPETITIVE POSITIONING based on at least one named real alternative?** Generic "competitors charge more" is not positioning.
5. **If the product is internal or non-commercial: is that explicitly stated?** A blank pricing file is more dangerous than a "N/A" with a reason.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## PRICING MODEL
<!-- Free / freemium / subscription / one-time / usage-based / internal (N/A) -->
<!-- Justified by MODEL REASONING Step 3 — note any mismatch explicitly -->
<!-- Hard constraints from MODEL REASONING Step 5 noted here -->
{pricing_model}

## TIERS / PLANS
<!-- Pricing tiers with features and limits per tier -->
<!-- Each tier must be justified by a user segment, not just a price point -->
<!-- If no tiers: explain why a single price serves all segments -->
{pricing_tiers}

## PRICE RATIONALE
<!-- Why this price? What is the value exchange? -->
<!-- Must reference cost floor (Step 1) and value ceiling (Step 2) -->
{price_rationale}

## COMPETITIVE POSITIONING
<!-- Where does this price sit relative to alternatives? -->
<!-- Must name at least one real alternative (SELF-INTERROGATION question 4) -->
{competitive_position}

## REVENUE SCENARIOS
<!-- Best / expected / minimum viable -->
<!-- Each scenario must include: user count · price · monthly revenue · covers costs? -->
<!-- Stress-tested in MODEL REASONING Step 4 -->
{revenue_scenarios}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 1 found no cost floor in [[07_BUDGET]],
     or if SELF-INTERROGATION question 3 found an unrealistic minimum viable scenario.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 03_NEXT_STEPS
<!-- Pre-generation: asked to 03_NEXT_STEPS before this file was written -->
> "What is the single action that, if done now, reduces the most uncertainty
>  about whether this project is viable?"
>
> Answer received: {inference_answer_from_03}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 07_BUDGET
<!-- Answer provided to 07_BUDGET during inference loop -->
> Question received: "Given the budget, what is the minimum price that keeps this sustainable?
>  Is the target price coherent with the product's value and the market?"
> Answer given: {inference_answer_to_07}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[07_BUDGET]] for production costs that constrain pricing
→ see [[02_PRODUCT]] for features that justify the price
→ see [[08_LIMITS]] for pricing constraints
<!-- Add here: any competitive positioning gap from SELF-INTERROGATION question 4 → [[12_ASKED]] -->
```
