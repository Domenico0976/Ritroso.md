# Template: 06_PRICE.md

> Answers: HOW MUCH does it cost for the user?
> Inference loop: receives question from 07_BUDGET, asks 03_NEXT_STEPS.

---

```markdown
# 06_PRICE.md — {project_name}
{standard_header}

## PRICING MODEL
<!-- Free, freemium, subscription, one-time, usage-based, internal -->
{pricing_model}

## TIERS / PLANS
<!-- Pricing tiers with features and limits per tier -->
{pricing_tiers}

## PRICE RATIONALE
<!-- Why this price? What is the value exchange? -->
{price_rationale}

## COMPETITIVE POSITIONING
<!-- Where does this price sit relative to alternatives? -->
{competitive_position}

## REVENUE SCENARIOS
<!-- Best case / expected / minimum viable -->
{revenue_scenarios}

## INFERENCE QUESTION → 03_NEXT_STEPS
<!-- Pre-generation: asked to 03_NEXT_STEPS before this file was written -->
> "What is the single action that, if done now, reduces the most uncertainty
>  about whether this project is viable?"
>
> Answer received: {inference_answer_from_03}

## INFERENCE ANSWER ← 07_BUDGET
<!-- Answer provided to 07_BUDGET during inference loop -->
> Question received: "Given the budget, what is the minimum price that keeps this sustainable?
>  Is the target price coherent with the product's value and the market?"
> Answer given: {inference_answer_to_07}

## DEPENDENCIES
→ see [[07_BUDGET]] for production costs that constrain pricing
→ see [[02_PRODUCT]] for features that justify the price
→ see [[08_LIMITS]] for pricing constraints
```
