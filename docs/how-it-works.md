# How Ritroso.md Works — Technical Documentation v3

## Overview

Ritroso.md is a **reasoning protocol** for LLMs, not just a prompt template.
It forces the model through four distinct phases before producing any output:

1. **Context acquisition** — prompt capture + gap detection + questions
2. **Pre-generation inference** — files interrogate each other before being written
3. **Multimodal resource mapping** — scanning all available context
4. **Generation + negative verification** — write, then attack your own output

---

## Phase 1: Context Acquisition

### Prompt Capture
The full prompt is saved unchanged to `.ritroso_prompt_cache.tmp`.
This is the ground truth — every verification step refers back to it.

### Domain Classification
The model identifies the primary domain from 9 slug categories and extracts
a `project_name_slug` (max 3 words, kebab-case).

### Context Gap Detection
The model evaluates 5 gap dimensions:
- `goal_gap` — does the prompt state WHY?
- `constraint_gap` — are limits known?
- `antipattern_gap` — are failure modes known?
- `user_gap` — are actors/personas defined?
- `stack_gap` — is the technology known?

For each gap, one question is asked — in priority order — before proceeding.
Minimum viable context requires `goal_gap` and `constraint_gap` resolved.

---

## Phase 2: Inference Loop

This is the core innovation of v3.

Before writing any file, the model runs a **pre-generation reasoning chain**:
each file's future content is represented as a question that the next file
must answer. The chain creates 8 resolved assumptions and surfaces
open questions before a single character of output is written.

### Why This Matters

Without the inference loop, files are generated in sequence with only
the prompt as context. Each file can develop independently, creating
*locally coherent but globally inconsistent* output.

With the inference loop:
- 01_GOAL knows what 02_PRODUCT will say before 02_PRODUCT is written
- 10_ERROR knows which component fails before 05_COMPONENTS is written
- 07_BUDGET knows the real agent cost before 09_AGENTS is written

This is **inference before generation** — coherence is built, not checked.

### Loop Chain
```
01→02→04→05→10→08→09→07→06→03→11→12
```
Each arrow represents a question asked and an answer received.
The output is 8+ resolved assumptions and a structured set of cross-file signals.

---

## Phase 3: Resource Mapping

The `context-mapper` skill scans all available resources:
- Reads and summarizes markdown files
- Extracts structure from source code
- Notes presence of images, PDFs, video
- Identifies context gaps based on domain
- Produces a `INFERRED_SIGNALS` section from existing resources

Resources with `context_weight: HIGH` are processed first.
The resource map is injected into every file during generation.

---

## Phase 4: Generation + Negative Verification

### Generation
13 files are generated in order (01 → 12, then 00_INDEX last).
Each file:
- Uses the standard header from `prompts/project-template.md`
- Injects inference loop resolved assumptions as `[INF]` values
- Documents its own inference question and the answer it received
- Runs an inner monologue before moving to the next file

### Negative Verification
After generation, the model applies the negative prompt:
- Starts from the assumption that the output is wrong
- Verifies 7 axes including inference loop consistency
- Corrects inline with `[CORRECTION]` prefix
- Marks `[RITROSO-VERIFIED]` only if all axes pass

---

## Key Flags

| Flag | Meaning |
|------|---------|
| `[OPEN]` | Unresolved assumption or missing information |
| `[OPEN-INF-N]` | Gap unresolved during inference loop |
| `[ASSUME-N]` | Assumption not confirmed by user |
| `[INF]` | Value derived from inference loop, not stated in prompt |
| `[ASKED]` | Value from context gap question answer |
| `[CORRECTION]` | Correction applied during negative verification |
| `[RITROSO-VERIFIED]` | Full set passed negative verification |

---

## File Dependency Graph

```
01_GOAL ←→ 02_PRODUCT ←→ 04_ELEMENTS ←→ 05_COMPONENTS
    ↕              ↕              ↕              ↕
08_LIMITS ←→ 10_ERROR          09_AGENTS ←→ 07_BUDGET
    ↕              ↕              ↕              ↕
03_NEXT_STEPS ←→ 11_INTERPOLATION ←→ 06_PRICE
                        ↕
                  12_ASKED
                        ↕
                  00_INDEX (last)
```

Every arrow is bidirectional because the inference loop and wikilinks
create two-way awareness between all files.
