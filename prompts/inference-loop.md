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

## Loop Definition

### 01_GOAL asks:
```
Question to 02_PRODUCT:
"Does the product as you imagine it actually solve the goal stated?
 What is the most likely misalignment between goal and product?"
```

### 02_PRODUCT answers + asks:
```
Answer: [product → goal coherence assessment]

Question to 04_ELEMENTS:
"What are the minimum elements required for this product to function at all?
 What element, if missing, makes the product non-functional?"
```

### 04_ELEMENTS answers + asks:
```
Answer: [minimum viable elements list]

Question to 05_COMPONENTS:
"Which components are needed to implement these elements?
 Are there components that would be used by more than half of the elements?"
```

### 05_COMPONENTS answers + asks:
```
Answer: [components assessment]

Question to 10_ERROR:
"Which of these components is the most likely single point of failure?
 What breaks first when this component fails?"
```

### 10_ERROR answers + asks:
```
Answer: [failure analysis]

Question to 08_LIMITS:
"Given these failure modes, what must be explicitly prohibited
or constrained to prevent systemic failure?"
```

### 08_LIMITS answers + asks:
```
Answer: [constraints derived from failure modes]

Question to 09_AGENTS:
"Who is responsible for monitoring these limits and responding to failures?
 Is there an agent (AI or human) capable of doing this with current resources?"
```

### 09_AGENTS answers + asks:
```
Answer: [agents and responsibilities]

Question to 07_BUDGET:
"What is the real cost of operating these agents at the defined limits?
 Is there a scenario where the budget is exceeded by normal operations?"
```

### 07_BUDGET answers + asks:
```
Answer: [budget feasibility]

Question to 06_PRICE:
"Given the budget, what is the minimum price that keeps this sustainable?
 Is the target price coherent with the product's value and the market?"
```

### 06_PRICE answers + asks:
```
Answer: [pricing coherence]

Question to 03_NEXT_STEPS:
"What is the single action that, if done now, reduces the most uncertainty
about whether this project is viable?"
```

### 03_NEXT_STEPS answers + asks:
```
Answer: [highest-leverage first action]

Question to 11_INTERPOLATION:
"What is the most critical dependency in the system?
 If this dependency breaks, what cascades?"
```

### 11_INTERPOLATION answers + asks:
```
Answer: [critical dependency map]

Question to 12_ASKED:
"After this entire inference chain, what is the most important question
that still has no answer and would most change the design if answered?"
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
      resolution: "{{ 02_PRODUCT answer }}"
    - dimension: minimum_viable_elements
      resolution: "{{ 04_ELEMENTS answer }}"
    - dimension: critical_failure_point
      resolution: "{{ 10_ERROR answer }}"
    # ... etc
  open_questions:
    - id: OPEN-INF-1
      question: "{{ 12_ASKED answer }}"
      impact: HIGH
  cross_file_signals:
    - signal: "Budget may not cover agent operations → price needs revision"
      affects: [07_BUDGET, 06_PRICE, 09_AGENTS]
    - signal: "Single point of failure in {{ component }} → limits must cover it"
      affects: [05_COMPONENTS, 08_LIMITS, 10_ERROR]
```

---

## Rules

1. The loop runs entirely in the model's internal reasoning — no output is written yet
2. Each answer is a **compressed inference**, not a full file draft
3. If a file cannot answer a question (gap) → log as `[OPEN-INF-N]` and continue
4. The loop output is injected as context into every file during generation
5. If the inference loop reveals a critical contradiction → trigger `context_gap_questions` again
6. The loop can run **multiple passes** if contradictions are found (max 3 passes)

---

## Post-Generation Loop (Negative Verification)

After generation, the same questions are asked again — this time against the
actual written files:

```
For each file:
  - Does the written content match the inference loop answer?
  - Does it contradict any cross_file_signal?
  - Are all [OPEN-INF-N] items present in 12_ASKED.md?

→ if mismatch found: [CORRECTION] + fix inline
→ if all match: [RITROSO-VERIFIED]
```
