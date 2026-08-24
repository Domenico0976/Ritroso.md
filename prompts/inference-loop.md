# Inference Loop Protocol

> The inference loop runs BEFORE file generation.
> Files interrogate each other to build coherence before any output is written.
> The model reasons with itself.

---

## Core Concept

Each file in the 13-file set has a **question it must answer** and a **question it asks**
to the next file. This creates a chain of inference:

```
01_GOAL asks →
  02_PRODUCT answers + asks →
    04_ELEMENTS answers + asks →
      05_COMPONENTS answers + asks →
        10_ERROR answers + asks →
          08_LIMITS answers + asks →
            09_AGENTS answers + asks →
              07_BUDGET answers + asks →
                06_PRICE answers + asks →
                  03_NEXT_STEPS answers + asks →
                    11_INTERPOLATION answers + asks →
                      12_ASKED answers
```

Each file's answer becomes **input context** for the generation of that file.
This is not sequential generation — it is **pre-generation inference**.

---

## Answer Quality Contract

**Every answer in the loop MUST satisfy ALL of the following — no exceptions:**

| Criterion | Rule | Fail condition |
|-----------|------|----------------|
| **Specificity** | Uses project-specific nouns, not generic terms | Answer contains "something", "things", "some component", "the system" with no referent |
| **Falsifiability** | Could be proved wrong by a different project | Answer would be true for any project |
| **Actionability** | Names a concrete object, person, decision, or threshold | Answer is a category without an instance |
| **Cross-reference** | References at least one other file by name | Answer is self-contained with no file link |
| **Length** | Minimum 2 sentences, maximum 6 | Single-word or one-liner answers |

If an answer fails any criterion → it is treated as a **gap** → log as `[OPEN-INF-N]` and continue.

---

## Loop Definition

### 01_GOAL asks:
```
Question to 02_PRODUCT:
"Does the product as you imagine it actually solve the goal stated?
What is the most likely misalignment between goal and product?"

Required answer format:
- Line 1: YES/PARTIAL/NO — with one-sentence reason tied to the specific goal
- Line 2: The most concrete misalignment risk (name the dimension: scope, user, timing, resource)
- Line 3: Which file should own the fix → [[XX_FILE]]
```

### 02_PRODUCT answers + asks:
```
Answer: [product → goal coherence assessment — must name the specific goal and product]

Question to 04_ELEMENTS:
"What are the minimum elements required for this product to function at all?
What element, if missing, makes the product non-functional?"

Required answer format:
- List of 3–7 minimum elements, each named specifically (not "a database" but "user session store")
- One element flagged as CRITICAL with reason
- Confidence: HIGH / MEDIUM / LOW with reason if not HIGH
```

### 04_ELEMENTS answers + asks:
```
Answer: [minimum viable elements — must use project vocabulary, not generic terms]

Question to 05_COMPONENTS:
"Which components are needed to implement these elements?
Are there components that would be used by more than half of the elements?"

Required answer format:
- One component per element (or shared component if covers multiple)
- Shared components explicitly flagged with count: "used by N/M elements"
- Architecture pattern named (e.g. event-driven, layered, microservice, monolith)
```

### 05_COMPONENTS answers + asks:
```
Answer: [components assessment — must name real libraries/patterns, not abstract types]

Question to 10_ERROR:
"Which of these components is the most likely single point of failure?
What breaks first when this component fails?"

Required answer format:
- Component name + failure mode (specific: "X fails when Y because Z")
- Downstream cascade: what other components are affected and in what order
- Detection method: how would this failure be observable
```

### 10_ERROR answers + asks:
```
Answer: [failure analysis — must be a named component with named cascade, not generic]

Question to 08_LIMITS:
"Given these failure modes, what must be explicitly prohibited
or constrained to prevent systemic failure?"

Required answer format:
- Each limit tied to a specific failure mode from 10_ERROR
- Limit format: WHAT is prohibited · WHY (failure mode) · CONSEQUENCE if violated
- At least one hard limit and one soft limit
```

### 08_LIMITS answers + asks:
```
Answer: [constraints derived from failure modes — each limit must reference its failure source]

Question to 09_AGENTS:
"Who is responsible for monitoring these limits and responding to failures?
Is there an agent (AI or human) capable of doing this with current resources?"

Required answer format:
- One named agent per limit (or explicit gap if no agent can own it)
- Capability assessment: can this agent actually do this given the declared constraints?
- If NO capable agent exists → flag as [OPEN-INF-N] with impact: CRITICAL
```

### 09_AGENTS answers + asks:
```
Answer: [agents and responsibilities — must name real roles, not "a developer" or "admin"]

Question to 07_BUDGET:
"What is the real cost of operating these agents at the defined limits?
Is there a scenario where the budget is exceeded by normal operations?"

Required answer format:
- Cost per agent per unit (hour/month/request) — even rough estimates
- Break-even scenario: at what load does cost exceed budget?
- YES/NO: can the declared budget sustain this agent setup?
```

### 07_BUDGET answers + asks:
```
Answer: [budget feasibility — must include at least one numeric estimate, even rough]

Question to 06_PRICE:
"Given the budget, what is the minimum price that keeps this sustainable?
Is the target price coherent with the product's value and the market?"

Required answer format:
- Minimum sustainable price (numeric range acceptable)
- Coherence verdict: YES/PARTIAL/NO with one-line reason
- If PARTIAL or NO → what must change (product scope / cost / positioning)
```

### 06_PRICE answers + asks:
```
Answer: [pricing coherence — must include a number or explicit "price not applicable" with reason]

Question to 03_NEXT_STEPS:
"What is the single action that, if done now, reduces the most uncertainty
about whether this project is viable?"

Required answer format:
- One specific action (verb + object, e.g. "validate payment integration with Stripe test keys")
- Uncertainty it resolves: which [OPEN-INF-N] or which cross_file_signal
- Estimated time to complete: hours/days
```

### 03_NEXT_STEPS answers + asks:
```
Answer: [highest-leverage first action — must be completable by one person in a bounded time]

Question to 11_INTERPOLATION:
"What is the most critical dependency in the system?
If this dependency breaks, what cascades?"

Required answer format:
- Dependency name (external service / internal module / human role)
- Cascade map: A breaks → B fails → C is blocked (at least 2 levels)
- Mitigation: is there a fallback? YES/NO/PARTIAL
```

### 11_INTERPOLATION answers + asks:
```
Answer: [critical dependency map — must name real services/modules, not abstract layers]

Question to 12_ASKED:
"After this entire inference chain, what is the most important question
that still has no answer and would most change the design if answered?"

Required answer format:
- One question only (the single most impactful)
- Impact: what would change if answered YES vs NO
- Which files it affects: [[XX_FILE]], [[YY_FILE]]
```

### 12_ASKED answers:
```
Answer: [the single most critical open question]

→ inference loop complete
→ output: resolved_assumptions + open_questions + cross_file_signals
```

---

## Loop Output Format

```yaml
inference_loop_output:
  resolved_assumptions:
    - dimension: goal_product_alignment
      resolution: "{{ specific project-bound assessment }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ which file/section supports this }}"
    - dimension: minimum_viable_elements
      resolution: "{{ named elements, not categories }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: critical_failure_point
      resolution: "{{ named component + named cascade }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: agent_budget_coherence
      resolution: "{{ named agents + numeric budget check }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: price_sustainability
      resolution: "{{ number or explicit N/A with reason }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: highest_leverage_action
      resolution: "{{ verb + object + time estimate }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: critical_dependency
      resolution: "{{ named dependency + cascade map }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
    - dimension: most_critical_open_question
      resolution: "{{ the question + YES/NO impact }}"
      confidence: HIGH | MEDIUM | LOW
      evidence: "{{ source }}"
  open_questions:
    - id: OPEN-INF-1
      question: "{{ specific question }}"
      impact: HIGH | MEDIUM | LOW
      affects: ["{{ file }}", "{{ file }}"]
      reason_unresolved: "{{ why it could not be inferred }}"
  cross_file_signals:
    - signal: "{{ concrete tension or dependency between files }}"
      type: CONTRADICTION | DEPENDENCY | RISK | ASSUMPTION
      affects: ["07_BUDGET", "06_PRICE", "09_AGENTS"]
      resolution: "{{ how files should handle this signal }}"
```

---

## Rules

1. The loop runs entirely in the model's internal reasoning — no output is written yet
2. Each answer is a **compressed inference**, not a full file draft
3. Every answer must pass the **Answer Quality Contract** above before being accepted
4. If a file cannot answer a question (gap) → log as `[OPEN-INF-N]` with `reason_unresolved` and continue
5. The loop output is injected as context into every file during generation
6. If the inference loop reveals a critical contradiction → trigger `context_gap_questions` again
7. The loop can run **multiple passes** if contradictions are found (max 3 passes)
8. A LOW confidence resolution is acceptable only if `reason_unresolved` explains why it cannot be HIGH
9. Generic answers that pass the loop are a protocol violation — they must be re-asked as a gap

---

## Post-Generation Loop (Negative Verification)

After generation, the same questions are asked again — this time against the
actual written files:

```
For each file:
  - Does the written content match the inference loop answer?
  - Does it contradict any cross_file_signal?
  - Are all [OPEN-INF-N] items present in 12_ASKED.md with reason_unresolved?
  - Does every [INF] tag in the file trace back to a resolved_assumption in the loop output?

→ if mismatch found: [CORRECTION] + fix inline + re-log in 00_INDEX correction count
→ if [INF] tag has no matching loop resolution: [CORRECTION] required
→ if all match: [RITROSO-VERIFIED]
```
