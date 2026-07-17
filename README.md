# Ritroso.md

> Contextual reasoning skill for LLMs with multimodal generation of complete `.md` file sets and retroactive self-verification.

## What It Is

**Ritroso.md** is a skill framework for LLM models that forces the model to:

1. Classify the project type and create a dedicated output folder
2. Generate a **complete set of 13 `.md` files** as a structured project map
3. Read every available resource in context in a **multimodal** way (text, code, images, PDF, GitHub repos)
4. Run an **inference loop**: each file actively interrogates the others to maximize cross-file coherence before generation
5. Submit to a **negative self-verification prompt** — the model attacks its own output and must defend it with cross-file consistency

The name "Ritroso" means the movement: **the model goes forward, then turns back to verify**.

---

## Structure

```
Ritroso.md/
├── skills/
│   ├── RITROSO.md              # Main skill — complete flow (markdown + YAML frontmatter)
│   └── context-mapper.yaml     # Auxiliary skill — multimodal resource mapping
├── prompts/
│   ├── negative-verification.md    # Negative self-verification prompt
│   ├── project-template.md         # Shared standard header for the file set
│   ├── context-gap-questions.md    # Protocol for empty/incomplete context
│   ├── inference-loop.md           # Inter-file questioning loop protocol
│   └── file-set-templates/         # Templates for each of the 13 set files
│       ├── 00_INDEX.md
│       ├── 01_GOAL.md
│       ├── 02_PRODUCT.md
│       ├── 03_NEXT_STEPS.md
│       ├── 04_ELEMENTS.md
│       ├── 05_COMPONENTS.md
│       ├── 06_PRICE.md
│       ├── 07_BUDGET.md
│       ├── 08_LIMITS.md
│       ├── 09_AGENTS.md
│       ├── 10_ERROR.md
│       ├── 11_INTERPOLATION.md
│       └── 12_ASKED.md
├── new-ideas/                  # Generated outputs — organized by domain/project
│   └── {domain-slug}/
│       └── {project-slug}/
│           ├── 00_INDEX.md
│           └── ... (13 files total)
└── docs/
    └── how-it-works.md         # Technical documentation of the v3 flow
```

---

## Quick Flow v3

```
[USER PROMPT]
      ↓
[SAVE FULL PROMPT] → .ritroso_prompt_cache.tmp
      ↓
[CLASSIFY DOMAIN] → domain_slug + project_name_slug
      ↓
[CONTEXT CHECK] ─── empty context? ──→ [CONTEXT GAP QUESTIONS — 1 per gap, sequential]
      ↓
[INFERENCE LOOP] → files interrogate each other before generation
      ↓
[CONTEXT MAPPER multimodal] → text · code · img · pdf · repo
      ↓
[GENERATE 13 .MD FILES] → 01_GOAL → 02_PRODUCT → ... → 12_ASKED
      (each file runs inner monologue + asks next file a question)
      ↓
[NEGATIVE PROMPT] → "you got it wrong — verify cross-file"
      ↓
[00_INDEX.md] + [RITROSO-VERIFIED]
```

---

## The 13 Files of the Set

| # | File | Answers |
|---|------|---------|
| 00 | `INDEX` | Navigation map — generated last |
| 01 | `GOAL` | Why the project exists |
| 02 | `PRODUCT` | What it does in detail |
| 03 | `NEXT_STEPS` | What to do now, ordered P1/P2/P3 |
| 04 | `ELEMENTS` | What the system contains |
| 05 | `COMPONENTS` | How it is built |
| 06 | `PRICE` | How much it costs for the user |
| 07 | `BUDGET` | How much it costs to produce |
| 08 | `LIMITS` | What cannot/must not be done |
| 09 | `AGENTS` | Who does what (AI + humans) |
| 10 | `ERROR` | What can go wrong |
| 11 | `INTERPOLATION` | How everything connects |
| 12 | `ASKED` | What we don't know yet |

---

## Inference Loop — Core Concept

Before generating each file, the model runs an **inter-file interrogation**:

> Each file asks a question to the next file — and the next file must answer
> before it is written. This creates a chain of inference that forces coherence
> before the output exists.

Example chain:
```
01_GOAL asks → "Does the product actually solve this goal?"
02_PRODUCT answers → then asks → "Are the components sufficient to build this?"
05_COMPONENTS answers → then asks → "What breaks if this component fails?"
10_ERROR answers → ...
```

The loop runs **before generation** and **after generation** (negative verification).
This is the mechanism that prevents hallucinated consistency.

---

## Context Gap Protocol

If the prompt is **empty or insufficient**, the model does NOT hallucinate.
Instead, it triggers the **context gap question protocol**:

1. Identify the most critical missing dimension (one at a time)
2. Ask exactly **one question** — the most blocking one
3. Wait for user answer → merge into context
4. Re-evaluate: are there still gaps? → ask next question
5. Once minimum viable context is reached → start inference loop

The model never skips this. A hallucinated context is worse than an empty one.

---

## How to Use

1. Load the skill [`skills/RITROSO.md`](./skills/RITROSO.md) into your skill system
   (e.g. GitHub Copilot, OpenDevin, Continue, or custom agent)
2. Provide a project prompt
3. The system automatically classifies the domain and creates the output folder
4. If critical context is missing, you will be asked **one question at a time**
5. You will find the 13 `.md` files generated in `new-ideas/{domain}/{project}/`
6. The entire set is auto-verified with the negative prompt before delivery

---

## Domain Types Supported

`design-campaign` · `web-development` · `ai-agent` · `product-strategy`
`content-marketing` · `infrastructure` · `business-plan` · `research` · `generic`

---

## Philosophy

> *"The model must be wrong before it can be right."*

The negative prompt is not destructive — it is the mechanism that transforms
a mediocre output into a coherent one. The LLM is forced to compare its work
with the original prompt from **hostile angles** (sales, time, resources, logic errors,
cross-file contradictions).

In v3, the inference loop adds a second layer: **coherence is built before generation,
not just verified after**. Files interrogate each other. The model reasons with itself.
