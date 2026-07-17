---
name: ritroso
version: "3.0.0"
author: Domenico Fusto
tags:
  - reasoning
  - context
  - self-verification
  - project-planning
  - multimodal
  - llm-skill
  - inference-loop
  - context-gap
uses:
  - skills/context-mapper.yaml
  - prompts/context-gap-questions.md
  - prompts/inference-loop.md
variables:
  output_base_folder: new-ideas
  temp_prompt_file: .ritroso_prompt_cache.tmp
  negative_prompt_ref: prompts/negative-verification.md
  file_set_ref: prompts/file-set-templates/
  inference_loop_ref: prompts/inference-loop.md
  context_gap_ref: prompts/context-gap-questions.md
---

# RITROSO — Contextual Reasoning Skill v3

> Contextual reasoning skill for LLMs with multimodal generation
> of complete `.md` file sets, inference loop, and retroactive self-verification.

**When you receive a prompt, execute these steps in order:**

1. Classify the request type (domain)
2. Create a dedicated folder `new-ideas/{domain_slug}/{project_name_slug}/`
3. Run the **context gap protocol** — if context is empty or insufficient, ask questions
4. Run the **inference loop** — files interrogate each other before generation
5. Generate a **complete set of 13 `.md` files** for that project
6. Self-verify the entire set with the negative prompt before delivering output

The system is **multimodal**: reads text, code, images, PDFs, video,
GitHub repos and any resource available in context.

---

## Domain Types

Used to classify the domain and name the output folder.

| Slug | Keywords |
|------|----------|
| `design-campaign` | campaign, graphic, visual, brand, poster, mascot, layout, UI, UX |
| `web-development` | app, site, frontend, backend, API, database, Next.js, React, PHP |
| `ai-agent` | agent, orchestration, LLM, model, pipeline, skill |
| `product-strategy` | product, MVP, roadmap, feature, launch, positioning |
| `content-marketing` | content, post, social, SEO, copy, article, newsletter |
| `infrastructure` | deploy, docker, CI/CD, cloud, server, DNS, infrastructure |
| `business-plan` | business, budget, revenue, costs, pricing, investment |
| `research` | research, analysis, benchmark, study, comparison, report |
| `generic` | *(fallback if no domain is identified)* |

---

## Step 1 — Prompt Reception and Saving

```yaml
id: receive_prompt
action: capture_input
save_to: .ritroso_prompt_cache.tmp
```

Receive the **ENTIRE** user prompt and save it unchanged to the temp file.

- **DO NOT** summarize
- **DO NOT** correct
- **DO NOT** paraphrase

The prompt is reused literally in the final negative prompt.

---

## Step 1b — Domain Classification and Folder Naming

```yaml
id: classify_domain
depends_on: [receive_prompt]
action: classify
output:
  domain_slug: "{{ classified_domain }}"
  output_folder: "new-ideas/{{ domain_slug }}/{{ project_name_slug }}"
```

Analyze the prompt to identify the **primary domain** from the Domain Types table:

- If the prompt touches multiple domains → choose the most dominant one
- If no domain is clear → use `generic`
- Extract `project_name_slug` from the prompt: max 3 words, kebab-case, lowercase

**Example:**
```
prompt: "I want a campaign for Neeo"
→ domain_slug:    design-campaign
→ output_folder:  new-ideas/design-campaign/neeo-campaign/
```

---

## Step 2 — Context Gap Detection

```yaml
id: context_gap_detection
depends_on: [classify_domain]
action: evaluate_context
evaluation_criteria:
  - domain_identified: bool
  - goal_known: bool
  - constraints_known: bool
  - anti_patterns_known: bool
  - resources_available: bool
  - enough_for_inference_loop: bool
on_empty_context:
  next_step: context_gap_questions
on_partial_context:
  next_step: context_gap_questions
on_sufficient_context:
  next_step: inference_loop
```

### Context Evaluation Rules

- **Empty context**: prompt has fewer than 3 meaningful signals → trigger questions
- **Partial context**: domain known but goal, constraints or anti-patterns missing → trigger questions
- **Sufficient context**: enough to run the inference loop without hallucination
- **Rich context**: existing files in repo → prioritize reading them before questions

The model NEVER hallucinates a context. Missing context = questions, not assumptions.

---

## Step 2.5 — Context Gap Questions

```yaml
id: context_gap_questions
action: generate_question
protocol_ref: prompts/context-gap-questions.md
sequence: one_at_a_time
after_each_answer:
  action: merge_context
  re_evaluate: true
  next_step: inference_loop_or_next_question
```

If context is empty or insufficient, apply the **context gap question protocol**:

1. Identify the **most blocking** missing dimension
2. Ask **exactly ONE question** — the one that, once answered, unblocks the most
3. Wait for user answer → merge into context
4. Re-evaluate: more gaps? → next question
5. Minimum viable context reached? → proceed to inference loop

```
To generate the complete file set for your project I need to
understand one critical aspect:

{{ generated_question }}

(Even a short answer is sufficient.)
```

**Never ask about**: style preferences, aesthetic choices, optional details.
**Always ask about**: goals, constraints, anti-patterns, technical limits, scope.

---

## Step 3 — Inference Loop (Pre-Generation)

```yaml
id: inference_loop
depends_on: [context_gap_detection]
action: invoke_protocol
protocol_ref: prompts/inference-loop.md
runs: before_generation
output:
  inferred_context: "{{ inference_loop.output }}"
  cross_file_questions: "{{ inference_loop.questions }}"
  resolved_assumptions: "{{ inference_loop.resolutions }}"
```

Before generating any file, run the **inference loop**:

- Each file's future content is sketched as a question
- Each question is passed to the next file in the chain
- The next file must answer before it is written
- Contradictions found here are resolved before they exist in written form

This is the mechanism that prevents hallucinated consistency.
See `prompts/inference-loop.md` for the full protocol.

---

## Step 4 — Resource Mapping (Multimodal)

```yaml
id: map_resources
depends_on: [inference_loop]
action: invoke_skill
skill: context-mapper
output:
  resource_map: "{{ context_mapper.output }}"
```

Invoke `context-mapper` to scan **all** available resources:

| Type | Extensions | Action |
|------|-----------|--------|
| Text | `.md` `.txt` `.pdf` | read and summarize |
| Code | `.ts` `.js` `.py` `.php` | extract structure, functions, types |
| Config | `.json` `.yaml` `.env` | read keys and dependencies |
| Images | `.png` `.jpg` `.svg` | note presence, name, folder |
| Video | `.mp4` `.mov` | note presence |
| Remote repos | GitHub URL in prompt | read root structure |

Each resource is evaluated for relevance against the `domain_slug`.

---

## Step 5 — Generate Complete 13-File Set

```yaml
id: generate_file_set
depends_on: [map_resources]
action: generate_file_set
output_folder: "{{ output_folder }}"
templates_ref: prompts/file-set-templates/
inference_context_ref: "{{ inference_loop.output }}"
```

Create the output folder and generate all files of the set:

| File | Answers |
|------|--------|
| `00_INDEX.md` | Navigation map — generated **last** |
| `01_GOAL.md` | Why the project exists |
| `02_PRODUCT.md` | What it does in detail |
| `03_NEXT_STEPS.md` | What to do now, ordered P1/P2/P3 |
| `04_ELEMENTS.md` | What the system contains |
| `05_COMPONENTS.md` | How it is built |
| `06_PRICE.md` | How much it costs for the user |
| `07_BUDGET.md` | How much it costs to produce |
| `08_LIMITS.md` | What cannot/must not be done |
| `09_AGENTS.md` | Who does what (AI + humans) |
| `10_ERROR.md` | What can go wrong |
| `11_INTERPOLATION.md` | How everything connects |
| `12_ASKED.md` | What we don't know yet |

### Generation Rules

1. Each file uses the standard header from `prompts/project-template.md`
2. Content is adapted to the classified `domain_slug`
3. Use the fastest format for yourself: compressed strings, `→` symbols, token-friendly notation
4. Each file contains `[[wikilink]]` to the other 12 files of the set
5. If a section lacks context → write `N/A: [reason]` + `[OPEN]`
6. No duplication: if content is already elsewhere → `→ see [[file]]`
7. Inject inferred context from the inference loop output

### Inner Monologue (active for every file)

Before moving to the next file, ask yourself:

- Is the file just written coherent with `01_GOAL.md`?
- Am I contradicting something in a previous file?
- Do the `[[wikilinks]]` point to files that exist in the set?
- What question does this file raise for the NEXT file? (→ inference loop)

→ If yes to any problem: correct **inline** before continuing.

---

## Step 6 — Negative Self-Verification Prompt

```yaml
id: negative_verification
depends_on: [generate_file_set]
action: self_verify
prompt_ref: prompts/negative-verification.md
inject_original_prompt: true
inject_full_file_set: true
verification_axes:
  - coherence_with_prompt
  - cross_file_consistency
  - logical_consistency
  - resource_feasibility
  - time_impact
  - business_impact
  - anti_pattern_violations
  - missing_critical_sections
  - wikilinks_validity
  - inference_loop_consistency
  - context_gap_coverage
```

Apply the negative prompt to the **entire file set**.
**Start from the assumption that YOU WERE WRONG.**

Verify each file on these axes:

- Coherence with the original user prompt (entire, from `.tmp`)
- Internal cross-file consistency across all 13 documents
- Feasibility of declared resources
- Real impact: time · sales · development · user's life
- Violated anti-patterns
- Files with unjustified `N/A`
- Wikilinks pointing to non-existent files
- Inference loop answers that contradict the final written content

→ If errors found: fix the offending file and log `[CORRECTION]`  
→ If everything holds: mark `[RITROSO-VERIFIED]` on the entire folder

---

## Step 7 — Final Output

```yaml
id: final_output
depends_on: [negative_verification]
action: finalize
generate_index: true
index_file: 00_INDEX.md
cleanup:
  - .ritroso_prompt_cache.tmp
mark_verified: true
verified_tag: "[RITROSO-VERIFIED]"
```

Finalize the output:

1. Add `[RITROSO-VERIFIED]` to the header of every file in the set
2. Generate `00_INDEX.md` with file list + correction count + open questions
3. Clean up `.ritroso_prompt_cache.tmp`
4. Return the path of the generated folder

---

## Philosophy

> *"The model must be wrong before it can be right."*

In v3 the inference loop builds coherence **before** generation.
Files interrogate each other. The model reasons with itself.
The negative prompt then verifies that the generated output matches
what the inference loop predicted.

The system is never confident: every output is guilty until proven innocent.
