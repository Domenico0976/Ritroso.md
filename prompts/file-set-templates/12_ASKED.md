# Template: 12_ASKED.md

> The most honest file in the set.
> Answers: WHAT DO WE NOT KNOW YET (questions, assumptions, conflicts, deferred items).
> Inference loop: closes the chain — answers the final question from 11_INTERPOLATION.
> Also collects all [OPEN-INF-N] items from the inference loop.

---

```markdown
---
name: "12_ASKED.md"
description: "Use this file to surface everything the project still does not know, cannot justify, or should not pretend is resolved. Interpret it as the uncertainty ledger for the whole file set, with special attention to unresolved inference gaps, risky assumptions, deferred decisions, and contradictions across files. Prefer explicit uncertainty over false confidence, because this file protects the agent from acting on invented certainty."
---

# 12_ASKED.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW.
     This file is generated LAST. By this point, all other files exist.
     This reasoning pass is a full audit, not a summary. -->

**Step 1 — Collect all [OPEN-INF-N] items from every file.**
Go through every generated file and list every [OPEN-INF-N] tag found.
For each: which file it came from, what question it represents, why it could not be resolved.
Do not paraphrase — use the exact question text from the source file.

**Step 2 — Find contradictions between files.**
Compare these pairs for coherence:
- [[01_GOAL]] success criteria ↔ [[02_PRODUCT]] core features (do features actually deliver the criteria?)
- [[07_BUDGET]] sustainability threshold ↔ [[06_PRICE]] revenue scenarios (do scenarios cover the threshold?)
- [[08_LIMITS]] HARD LIMITS ↔ [[05_COMPONENTS]] tech stack (does the stack violate any limit?)
- [[09_AGENTS]] CAPABILITY ASSESSMENT ↔ [[08_LIMITS]] AGENT LIMITS (are agents constrained consistently?)
- [[10_ERROR]] recovery protocols ↔ [[09_AGENTS]] ESCALATION PATHS (does every recovery have an owner?)
For each contradiction found: name the two files, the specific conflict, and which file should yield.

**Step 3 — List all assumptions made without user confirmation.**
For every section across all files: what was written based on inference rather than explicit user input?
Format: "File X assumed Y because Z" — be specific.
These are the highest-risk items in the entire set.

**Step 4 — Identify the single most critical unanswered question.**
From all open items, contradictions, and assumptions: which one, if answered incorrectly,
would cause the most rework across the most files?
This is the MOST CRITICAL OPEN QUESTION. It goes in its own section with full impact analysis.

**Step 5 — Check for BLOCKING QUESTIONS not yet answered.**
Scan every file for unanswered BLOCKING QUESTIONS.
If any exist → they must appear in REQUIRES USER INPUT with a note that they were generated but not resolved.
An unresolved blocking question means the file set is incomplete.

<!-- If this file is generated before other files exist, flag every section as [PENDING] and regenerate after all files are complete. -->

---

## SUB-AGENT QUERIES
<!-- This file queries all other sub-agents for their residual uncertainty. -->

**Query → inference-loop agent (final check):**
> "After the full inference loop and all file generation:
>  what is the single most important question that was never answered and would most change the design?
>  Name the files it affects and what would change if answered YES vs NO."
> Answer: {subagent_inference_loop_final_answer}

**Query → negative-verification agent (final audit):**
> "Looking at the complete file set: what assumption is most likely to be false?
>  What would a post-mortem say was the overlooked risk that caused the project to fail?"
> Answer: {subagent_negative_verification_final_answer}

**Query → context-gap agent (final sweep):**
> "After all files are generated: what context is still missing?
>  List only gaps that, if filled now, would change a decision already made in another file."
> Answer: {subagent_context_gap_final_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Are there more than 5 items in OPEN QUESTIONS?** If yes → the file set has too many unresolved items to be actionable. Identify which 2-3 are truly critical and mark the rest as LOW impact.
2. **Is MOST CRITICAL OPEN QUESTION genuinely the single most impactful?** If you listed it by default without reasoning → re-derive it from MODEL REASONING Step 4.
3. **Does DETECTED CONFLICTS have at least one entry?** A file set with zero contradictions was not cross-checked. If genuinely conflict-free → state explicitly why (e.g. "project is too early-stage to have conflicting constraints").
4. **Are all UNVERIFIED ASSUMPTIONS traceable to a specific file and section?** Floating assumptions with no source are not useful.
5. **Is every item in REQUIRES USER INPUT something that only a human can answer** — not something that could be inferred from available data? If a machine could answer it → remove it and document the inference instead.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## OPEN QUESTIONS [OPEN]
<!-- Everything the model does not know with certainty -->
<!-- Format: [OPEN-N] question · impact if unanswered (H/M/L) · file it affects most -->
<!-- Prioritized: H-impact items first -->
{open_questions}

## INFERENCE LOOP OPEN ITEMS
<!-- All [OPEN-INF-N] items unresolved during the inference loop — collected in MODEL REASONING Step 1 -->
<!-- Format: [OPEN-INF-N] exact question text · source file · reason unresolved -->
{inference_open_items}

## UNVERIFIED ASSUMPTIONS
<!-- Things assumed without explicit user confirmation — from MODEL REASONING Step 3 -->
<!-- Format: [ASSUME-N] assumption · source file + section · risk if assumption is false -->
{unverified_assumptions}

## DEFERRED DECISIONS
<!-- Choices that must be made but not now -->
<!-- Format: Decision · why deferred · trigger condition that forces resolution -->
{deferred_decisions}

## DETECTED CONFLICTS
<!-- Points where two files contradict each other — from MODEL REASONING Step 2 -->
<!-- Format: File A section ↔ File B section · specific conflict · resolution: which file should yield -->
<!-- SELF-INTERROGATION question 3: if zero conflicts, explain explicitly -->
{conflicts}

## REQUIRES USER INPUT
<!-- Clean list of what only the user can answer -->
<!-- Includes all unresolved BLOCKING QUESTIONS from MODEL REASONING Step 5 -->
<!-- Format: Question · which file it blocks · urgency (before generation / before build / before launch) -->
{needs_user_input}

## MOST CRITICAL OPEN QUESTION
<!-- The single question from MODEL REASONING Step 4 — [INF] required -->
<!-- Format:
     Question: {the question}
     Impact if YES: {what changes}
     Impact if NO: {what changes}
     Files affected: [[XX_FILE]], [[YY_FILE]]
     Urgency: before generation / before build / before launch -->
> {most_critical_open_question}

## POST-VERIFICATION NOTES
<!-- Filled by the negative-verification pass: what remained unresolved after verification -->
<!-- Format: [CORRECTION-N] or [OPEN-POST-N] · file · issue · resolution status -->
{post_verification_notes}

## INFERENCE ANSWER ← 11_INTERPOLATION
<!-- Answer provided to 11_INTERPOLATION during inference loop -->
> Question received: "What is the most important question that still has no answer
>  and would most change the design if answered?"
> Answer given: {inference_answer_to_11}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ this file is the last read in the negative prompt
→ every [OPEN] unresolved remains as [OPEN] in the final file
→ every conflict found generates [CORRECTION] in the corresponding file
→ every [OPEN-INF-N] from inference loop is surfaced here
<!-- Add here: result of SELF-INTERROGATION question 3 (conflict count or explicit zero-conflict explanation) -->
```
