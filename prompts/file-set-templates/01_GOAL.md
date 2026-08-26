# Template: 01_GOAL.md

> Answers: WHY does this project exist?
> First file generated. Sets the north star for all other files.
> Inference loop: asks 02_PRODUCT the alignment question before writing.

---

```markdown
---
name: "01_GOAL.md"
description: "Use this file to reason about the core objective of the project in its real operating context. Interpret all goals through the lens of user value, declared constraints, inferred limits, and what must explicitly remain out of scope. If ambition conflicts with feasibility, prioritize the goal that still survives technical, budget, time, and anti-pattern boundaries described across the linked files."
---

# 01_GOAL.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW.
     Do not skip. Do not summarize. Reason through each point fully. -->

**Step 1 — Restate the project in your own words.**
In 2–3 sentences, describe what this project is trying to achieve from the perspective of the end user.
Do not copy the project brief. Rephrase it. If you cannot, the goal is not clear enough → trigger blocking question.

**Step 2 — Identify the real problem behind the stated goal.**
What is the user actually struggling with before this project exists?
Is the stated goal the root cause, or a symptom of a deeper problem?
Name the root cause explicitly.

**Step 3 — Test the goal for feasibility.**
Given what you already know about the constraints (budget, team size, timeline, tech), is this goal achievable in full?
If not in full: what is the minimum version of this goal that is still meaningful?
Label it: FULL / PARTIAL / NEEDS SCOPING.

**Step 4 — Identify the highest-risk assumption in this goal.**
What single assumption, if false, would make this goal invalid?
This assumption must appear in the DEPENDENCIES section and in [[12_ASKED]].

<!-- Output of this reasoning becomes the basis for all sections below.
     If any step cannot be completed → log as [OPEN-INF-N] and continue. -->

---

## SUB-AGENT QUERIES
<!-- BEFORE writing the file, send these queries to the relevant sub-agents in the skill.
     Wait for answers. If a sub-agent is unavailable, mark answer as [UNAVAILABLE] and continue. -->

**Query → context-gap agent:**
> "What context is missing to define this goal precisely?
>  List only gaps that, if unfilled, would make this goal ambiguous or contradictory."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What would a goal that looks like this one but fails look like?
>  Name the two most common failure patterns for goals of this type."
> Answer: {subagent_negative_verification_answer}

**Query → 02_PRODUCT (pre-inference):**
> "Does the product as you imagine it actually solve this goal?
>  What is the most likely misalignment between goal and product?"
> Answer: {subagent_product_alignment_answer}

<!-- Sub-agent answers are injected as context into the sections below.
     Do not ignore them. Reference them explicitly where relevant. -->

---

## SELF-INTERROGATION
<!-- Answer each question before writing the sections below.
     These are not rhetorical — give a real answer for this specific project. -->

1. **Is the core objective a single sentence?** If it requires more than one sentence, it is two goals. Split or choose.
2. **Can the success criteria be measured?** If any criterion is purely qualitative, convert it or mark it as [UNVERIFIABLE].
3. **Do the NON-GOALS explicitly exclude the most tempting scope expansions?** Name at least one thing that would be easy to add but must not be added.
4. **Is there a stakeholder who could veto this project?** If yes, they must appear in STAKEHOLDERS with their veto condition.
5. **Does this goal survive the 08_LIMITS constraints?** Cross-check mentally: would this goal be blocked by any likely limit? If yes → add a note in DEPENDENCIES.

<!-- If any answer is NO or uncertain → flag inline with [OPEN-INF-N]. -->

---

## CORE OBJECTIVE
<!-- The single sentence that explains why this project exists -->
<!-- [INF] if derived from inference loop -->
<!-- Must survive MODEL REASONING Step 3 feasibility test -->
{core_objective}

## PROBLEM BEING SOLVED
<!-- What specific pain, gap or opportunity triggers this project -->
<!-- Root cause identified in MODEL REASONING Step 2 goes here -->
{problem_statement}

## SUCCESS CRITERIA
<!-- How do we know the project succeeded? Measurable where possible -->
<!-- Each criterion must have passed SELF-INTERROGATION question 2 -->
{success_criteria}

## NON-GOALS
<!-- What this project explicitly does NOT aim to solve -->
<!-- Must include the answer from SELF-INTERROGATION question 3 -->
{non_goals}

## STAKEHOLDERS
<!-- Who cares about this succeeding? Who loses if it fails? -->
<!-- Include any veto-holder identified in SELF-INTERROGATION question 4 -->
{stakeholders}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING or SUB-AGENT QUERIES returned a critical gap.
     Do not ask if information can be reasonably inferred.
     Format: numbered list. Stop generation and wait for answers before continuing. -->
<!-- Example:
     1. [BLOCKING] The goal mentions "real-time" but no latency threshold is defined.
        → What is the maximum acceptable delay in seconds before the user experience degrades?
     2. [BLOCKING] Two stakeholders are listed but their priorities conflict.
        → Who has final decision authority when goal and budget conflict? -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 02_PRODUCT
<!-- Pre-generation: asked to 02_PRODUCT before this file was written -->
> "Does the product as you imagine it actually solve this goal?
>  What is the most likely misalignment between goal and product?"
>
> Answer received: {inference_answer_from_02}

## DEPENDENCIES
→ see [[02_PRODUCT]] for what is built to achieve this goal
→ see [[08_LIMITS]] for constraints that bound this goal
→ see [[12_ASKED]] for unresolved questions about the goal itself
<!-- Add here: the highest-risk assumption from MODEL REASONING Step 4 -->
<!-- Add here: any 08_LIMITS conflict identified in SELF-INTERROGATION question 5 -->
```
