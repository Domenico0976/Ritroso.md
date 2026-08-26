# Template: 07_BUDGET.md

> Answers: HOW MUCH does it cost to produce?
> Inference loop: receives question from 09_AGENTS, asks 06_PRICE.

---

```markdown
---
name: "07_BUDGET.md"
description: "Use this file to interpret production and operating cost under the real limits of the current project. Read budget as a forcing function that exposes feasibility, agent affordability, architecture weight, and the mismatch between ambition and available resources. Prefer cost structures that support the goal with minimum waste, and treat any budget that only works under optimistic assumptions as a warning signal requiring explicit attention."
---

# 07_BUDGET.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Identify all cost-generating decisions already made.**
For each component in [[05_COMPONENTS]] TECH STACK: assign a rough monthly cost.
For each agent in [[09_AGENTS]] AGENT MAP: assign a rough cost per operation and per month.
This is your cost baseline. Everything else is refinement.

**Step 2 — Find the top 3 cost drivers.**
Rank by monthly cost. The top 3 items account for the majority of operating cost.
If any cost driver is unknown or unestimated → the budget is incomplete. Flag as [OPEN-INF-N].
A budget with more than 2 unknown line items cannot be validated.

**Step 3 — Calculate the sustainability threshold.**
At what monthly revenue (or funding level) does this project break even?
Formula: total monthly costs × safety multiplier (1.3 for early stage, 1.1 for stable).
If break-even requires revenue that [[06_PRICE]] cannot realistically generate → there is a structural problem. Flag it explicitly — do not hide it in optimistic scenarios.

**Step 4 — Stress-test at scale.**
At 10× current load: does cost scale linearly, sub-linearly, or super-linearly?
Super-linear cost growth (cost grows faster than revenue) is a business model problem.
Name the cost item that drives super-linear growth, if it exists.

**Step 5 — Identify the one budget decision that changes everything.**
If the team had to make one change to reduce costs by 30%+, what would it be?
This is the highest-leverage cost optimization. It does not need to be implemented now,
but it must be named so future decisions do not make it impossible.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What cost information is missing that would change the budget estimate by more than 20%?
>  List only gaps that would promote a cost item from UNKNOWN to a significant line item."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a budget that looks viable but fails in production look like?
>  Name the two most common budget failure patterns for projects at this stage and scale."
> Answer: {subagent_negative_verification_answer}

**Query → 09_AGENTS (agent cost):**
> "What is the real cost of operating each agent at the defined limits in 09_AGENTS?
>  Is there a scenario where normal agent operations alone exceed the declared budget?"
> Answer: {subagent_agents_cost_answer}

**Query → 06_PRICE (revenue check):**
> "Given the costs here, what is the minimum price that keeps this project sustainable?
>  Does the price in 06_PRICE cover the costs at the minimum viable user count?"
> Answer: {subagent_price_check_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every component in [[05_COMPONENTS]] have a cost estimate, even rough?** If not → budget is incomplete.
2. **Is the sustainability threshold a real number, not a range?** If it is a range, use the pessimistic end.
3. **Does the declared total budget cover at least 6 months of operations?** If not → the project has a runway problem. Flag it.
4. **Is there a cost item that grows super-linearly with usage** (MODEL REASONING Step 4)? If yes → it must appear in CRITICAL COST DRIVERS with a mitigation note.
5. **Is the highest-leverage cost optimization from MODEL REASONING Step 5 documented?** Even if not acted on now, it must be visible for future decisions.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## TOTAL ESTIMATED BUDGET
<!-- High / low range with rationale.
     Must reference the cost baseline from MODEL REASONING Step 1. -->
{total_budget}

## COST BREAKDOWN
<!-- Format: Category · Item · One-time / Monthly · Estimate -->
<!-- Every component from [[05_COMPONENTS]] and every agent from [[09_AGENTS]] must appear here or be explicitly excluded with a reason. -->
{cost_breakdown}

## TIMELINE BUDGET
<!-- Cost mapped to project phases or milestones -->
<!-- Each phase: duration · phase cost · cumulative cost -->
{timeline_budget}

## CRITICAL COST DRIVERS
<!-- Top 3 items that consume the most budget — from MODEL REASONING Step 2 -->
<!-- Each driver: item name · monthly cost · % of total · mitigation if super-linear growth -->
{critical_cost_drivers}

## SUSTAINABILITY THRESHOLD
<!-- Minimum viable revenue or funding to continue — calculated in MODEL REASONING Step 3 -->
<!-- Format: monthly cost total · safety multiplier · break-even revenue needed · months of runway at current budget -->
{sustainability_threshold}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 2 found more than 2 unknown cost drivers,
     or if SELF-INTERROGATION question 3 found a runway shorter than 3 months.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 06_PRICE
<!-- Pre-generation: asked to 06_PRICE before this file was written -->
> "Given the budget, what is the minimum price that keeps this sustainable?
>  Is the target price coherent with the product's value and the market?"
>
> Answer received: {inference_answer_from_06}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 09_AGENTS
<!-- Answer provided to 09_AGENTS during inference loop -->
> Question received: "What is the real cost of operating these agents at the defined limits?
>  Is there a scenario where the budget is exceeded by normal operations?"
> Answer given: {inference_answer_to_09}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[09_AGENTS]] for agent operation costs
→ see [[05_COMPONENTS]] for infrastructure costs
→ see [[06_PRICE]] for revenue against this budget
→ see [[08_LIMITS]] for budget constraints
<!-- Add here: super-linear cost driver from MODEL REASONING Step 4 → [[12_ASKED]] if unmitigated -->
<!-- Add here: highest-leverage cost optimization from MODEL REASONING Step 5 -->
```
