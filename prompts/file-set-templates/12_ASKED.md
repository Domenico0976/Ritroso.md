# Template: 12_ASKED.md

> The most honest file in the set.
> Answers: WHAT DO WE NOT KNOW YET (questions, assumptions, conflicts, deferred items).
> Inference loop: closes the chain — answers the final question from 11_INTERPOLATION.
> Also collects all [OPEN-INF-N] items from the inference loop.

---

```markdown
# 12_ASKED.md — {project_name}
{standard_header}

## OPEN QUESTIONS [OPEN]
<!-- Everything the model does not know with certainty -->
<!-- Format: [OPEN-N] question → impact if unanswered -->
{open_questions}

## INFERENCE LOOP OPEN ITEMS
<!-- All [OPEN-INF-N] items unresolved during the inference loop -->
<!-- Format: [OPEN-INF-N] question → which files are affected -->
{inference_open_items}

## UNVERIFIED ASSUMPTIONS
<!-- Things assumed without explicit user confirmation -->
<!-- Format: [ASSUME-N] assumption → source (prompt/inference/default) -->
{unverified_assumptions}

## DEFERRED DECISIONS
<!-- Choices that must be made but not now -->
{deferred_decisions}

## DETECTED CONFLICTS
<!-- Points where two files in the set contradict each other -->
{conflicts}

## REQUIRES USER INPUT
<!-- Clean list of what only the user can answer -->
{needs_user_input}

## MOST CRITICAL OPEN QUESTION
<!-- The single question from the inference loop final answer -->
<!-- [INF] — derived from 11_INTERPOLATION inference chain close -->
> {most_critical_open_question}
> Impact if unanswered: {impact}

## POST-VERIFICATION NOTES
<!-- Filled by the negative prompt: what remained unresolved after verification -->
{post_verification_notes}

## INFERENCE ANSWER ← 11_INTERPOLATION
<!-- Answer provided to 11_INTERPOLATION during inference loop -->
> Question received: "What is the most important question that still has no answer
>  and would most change the design if answered?"
> Answer given: {inference_answer_to_11}

## DEPENDENCIES
→ this file is the last read in the negative prompt
→ every [OPEN] unresolved remains as [OPEN] in the final file
→ every conflict found generates [CORRECTION] in the corresponding file
→ every [OPEN-INF-N] from inference loop is surfaced here
```
