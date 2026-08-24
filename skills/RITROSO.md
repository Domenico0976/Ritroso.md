# RITROSO — Contextual Reasoning Skill v4

> Contextual reasoning skill for LLMs with multimodal generation
> of complete `.md` file sets, inference loop, panel-of-agents validation, and retroactive self-verification.

---

## ⚠️ MANDATORY: Read PLAN.md before executing anything

Before running any step below, load and follow `skills/PLAN.md`.
PLAN.md is the execution protocol. This file (RITROSO.md) is the skill definition.
They are not interchangeable. PLAN.md defines **when** and **how**. RITROSO.md defines **what**.

> If PLAN.md is not available in context: stop and ask the user to provide it before continuing.

---

## Execution Order

**When you receive a prompt, execute these steps in order. Do not skip or reorder.**

### STEP 1 — PLAN.md Phase 0 (Prompt Intake & Classification)
Before generating anything:
- Classify the project domain (see PLAN.md § Phase 0)
- Count structural ambiguities
- If structural ambiguities ≥ 2 → trigger GATE 0: ask one blocking question, stop
- Only proceed when structural ambiguities < 2

### STEP 2 — PLAN.md Phase 1 (Inference Loop)
Run all inter-file inference questions internally before writing any file.
- Mark every assumption as `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]`
- Check goal compatibility: flag any `[GOAL-CONFLICT]` before proceeding
- If a goal conflict is found → trigger GATE 1: document it, do not proceed silently

### STEP 3 — Create output folder
Create a dedicated folder: `new-ideas/{domain_slug}/{project_name_slug}/`

### STEP 4 — Generate all 13 files with frontmatter
Generate the complete set of 13 `.md` files.
For every file:
- Add YAML frontmatter (see Dynamic File Frontmatter Rule below)
- Apply all Project Rules from PLAN.md § Project Rules relevant to that file type
- After drafting each file, run the Panel of Agents (PLAN.md § Phase 2)
  - If any agent raises a BLOCK → trigger GATE 2: regenerate the file, do not annotate it
  - If an agent raises a NOTE → log it in the file, do not block

### STEP 5 — PLAN.md Phase 3 (Negative Verification)
After all 13 files are drafted:
- Run goal attack, architecture attack, roadmap attack, assumption attack
- If any attack produces a new BLOCK-level finding → trigger GATE 3: regenerate affected files
- Add negative verification findings to `11_INTERPOLATION.md` and `03_NEXT_STEPS.md`

### STEP 6 — PLAN.md Phase 4 (File Closure & Index)
Write `00_INDEX.md` last.
Populate the Verification Status block with actual results.
Only mark the file set `RITROSO-VERIFIED` if all conditions in PLAN.md § Gate 4 are met.

---

## Dynamic File Frontmatter Rule

Every generated file in the final output folder MUST begin with:

```yaml
---
name: "{real_file_name}"
description: "Dynamic agent-facing description built from the file objective, current project context, inferred limits, declared constraints, anti-patterns, and intended outcome. It must explain how to read the file, what reasoning approach to prioritize, what limits matter most, and what to do if context is incomplete."
---
```

### Frontmatter Constraints
- `name` must exactly match the file name
- `description` must never be generic when context exists
- `description` must mention the file objective
- `description` must reference any active `[GOAL-CONFLICT]` or `[ASSUMED-NO-BASIS]` items relevant to that file
- `description` should guide the consuming agent's reasoning approach
- If context is weak, description must instruct the agent to rely on linked files and unresolved questions rather than hallucinating

---

## File Generation Rules (per file type)

During file generation, for each of the 13 files:

1. Compute the file objective
2. Collect the most relevant constraints from: prompt, context-gap answers, inference loop, linked files
3. Apply the Project Rules from PLAN.md specific to that file type (e.g. R-GOAL-1 for 01_GOAL.md)
4. Build the dynamic `description` for the frontmatter
5. Draft the file content
6. Run the Panel of Agents on the draft
7. If BLOCK → regenerate. If NOTE → log and continue.
8. Place frontmatter above the markdown title
9. After all 13 files: run Negative Verification (Phase 3)
10. Re-check that every `description` is coherent with the actual file content after verification

---

## Assumption Tagging Rule

Every assumption made during generation must be tagged:
- `[INFERRED-FROM-TEXT]` — supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support

Every `[ASSUMED-NO-BASIS]` item must:
1. Appear in `12_ASKED.md` with tag and justification
2. Have a corresponding risk entry in `10_ERROR.md`
3. List which files would need rewriting if the assumption is wrong

---

## Goal Conflict Rule

If two or more goals in `01_GOAL.md` are found to be in tension:
1. Tag the conflict as `[GOAL-CONFLICT: GOAL-A vs GOAL-B]`
2. Document it in `11_INTERPOLATION.md` with: what conflicts, why, and what the resolution options are
3. Do NOT silently resolve the conflict by choosing one goal over the other
4. Surface it to the user in `12_ASKED.md` as a structural question

---

## Panel of Agents — Quick Reference

Full rules in PLAN.md § Phase 2. Summary:

| Agent | Core Question | Blocks when... |
|-------|--------------|----------------|
| 🏛 ARCHITECT | Does this hold at scale or under change? | Comp contradicts limits; P1 needs P2 infra |
| 🎨 DESIGNER | Can anyone act on this without asking? | Step has no output; no user flow in product |
| ⚙️ PRAGMATIST | Is this buildable with declared resources? | P1 scope >50% over budget; P1 needs unresolved OPEN |
| ⚠️ RISK | What kills this in 30 days? | No human failure mode; ASSUMED item has no risk entry |

---

## Multimodal Input

The system is multimodal: reads text, code, images, PDFs, video,
GitHub repos and any resource available in context.
All inputs feed the inference loop and the context-gap protocol before generation.

---

## Version History
| Version | Change |
|---------|--------|
| v1 | Initial skill — 13 files, inference loop, frontmatter |
| v2 | Added context-gap protocol |
| v3 | Added negative verification, dynamic frontmatter constraints |
| v4 | Bound PLAN.md as mandatory protocol; added Panel of Agents, assumption tagging, goal conflict rule, gate system |
