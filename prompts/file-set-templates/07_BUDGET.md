# Template: 07_BUDGET.md

> Answers: HOW MUCH does it cost to produce?
> Inference loop: receives question from 09_AGENTS, asks 06_PRICE.

---

```markdown
# 07_BUDGET.md — {project_name}
{standard_header}

## TOTAL ESTIMATED BUDGET
<!-- High / low range with rationale -->
{total_budget}

## COST BREAKDOWN
<!-- Format: Category · Item · One-time / Monthly · Estimate -->
{cost_breakdown}

## TIMELINE BUDGET
<!-- Cost mapped to project phases or milestones -->
{timeline_budget}

## CRITICAL COST DRIVERS
<!-- Top 3 items that consume the most budget -->
{critical_cost_drivers}

## SUSTAINABILITY THRESHOLD
<!-- Minimum viable revenue or funding to continue -->
{sustainability_threshold}

## INFERENCE QUESTION → 06_PRICE
<!-- Pre-generation: asked to 06_PRICE before this file was written -->
> "Given the budget, what is the minimum price that keeps this sustainable?
>  Is the target price coherent with the product's value and the market?"
>
> Answer received: {inference_answer_from_06}

## INFERENCE ANSWER ← 09_AGENTS
<!-- Answer provided to 09_AGENTS during inference loop -->
> Question received: "What is the real cost of operating these agents at the defined limits?
>  Is there a scenario where the budget is exceeded by normal operations?"
> Answer given: {inference_answer_to_09}

## DEPENDENCIES
→ see [[09_AGENTS]] for agent operation costs
→ see [[05_COMPONENTS]] for infrastructure costs
→ see [[06_PRICE]] for revenue against this budget
→ see [[08_LIMITS]] for budget constraints
```
