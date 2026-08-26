# Template: 10_ERROR.md

> Answers: WHAT can go wrong?
> Inference loop: receives question from 05_COMPONENTS, asks 08_LIMITS.

---

```markdown
---
name: "10_ERROR.md"
description: "Use this file to reason about how the project can fail in practice, not just in theory. Interpret errors through the most fragile components, likely cascades, silent failures, operational blind spots, and the limits required to contain them. Prefer realistic failure modeling over optimistic assumptions, and treat hidden breakpoints or unowned recovery paths as critical risks that must shape the rest of the design."
---

# 10_ERROR.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Start from the SPOF identified in [[05_COMPONENTS]].**
Take the single point of failure named there. Assume it fails completely.
Trace the cascade: what fails first, what fails next, what the user experiences.
Do not model abstract failures — model the specific failure of a specific named component.

**Step 2 — Find failures that do not generate errors.**
These are SILENT FAILURES: the system continues running but produces wrong outputs.
Common patterns: stale cache served as fresh data, partial write treated as complete, event lost without acknowledgment, AI output accepted without validation.
For this specific project: which of these patterns is most likely? Name it with the component involved.

**Step 3 — Identify the failure with the longest recovery time.**
Not the most probable failure — the one that takes longest to recover from.
This determines the worst-case downtime and the urgency of the recovery protocol.
If recovery time is unknown → estimate pessimistically and flag as [OPEN-INF-N].

**Step 4 — Check for failure modes with no current recovery path.**
For each high-impact failure: is there a documented recovery protocol?
If no recovery path exists → this is an UNOWNED RECOVERY. Add to [[12_ASKED]] as blocking.
An unowned recovery for a high-impact failure is a CRITICAL gap.

**Step 5 — Validate: are all failure modes tied to a real component or agent?**
Generic failures ("the system crashes", "data is lost") are not useful.
Every failure mode must name: the component that fails, the trigger, and the observable symptom.
If you cannot name all three → the failure model is incomplete. Flag as [OPEN-INF-N].

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What failure modes are implied by the product's architecture and usage patterns
>  but have not been documented? List only failures that would be invisible without monitoring."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a failure model that looks complete but misses the most dangerous real-world failure look like?
>  Name the two most common failure modeling blind spots for systems of this type."
> Answer: {subagent_negative_verification_answer}

**Query → 05_COMPONENTS (SPOF check):**
> "Confirm the single point of failure identified in 05_COMPONENTS.
>  If it fails completely, trace the full cascade: what breaks, in what order, what the user sees."
> Answer: {subagent_spof_cascade_answer}

**Query → 09_AGENTS (recovery ownership):**
> "For each high-impact failure mode here: which agent in 09_AGENTS owns the recovery?
>  Are there failures with no named recovery owner?"
> Answer: {subagent_recovery_ownership_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every failure mode name a specific component and a specific trigger?** Generic failures are not actionable. Replace them.
2. **Is there at least one SILENT FAILURE documented?** Every system has them. If zero are listed → the failure model is optimistic.
3. **Does every HIGH-impact failure have a recovery protocol?** If not → unowned recovery. Flag in [[12_ASKED]].
4. **Is the failure with the longest recovery time identified** (MODEL REASONING Step 3)? Worst-case recovery time must be known before limits are set in [[08_LIMITS]].
5. **Are there failure modes that only affect a subset of users?** (Partial failures, regional outages, per-account bugs.) If yes → the failure model must include partial-failure modes, not just total failures.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## FAILURE MODE MAP
<!-- All known failure modes with probability and impact -->
<!-- Format: Failure (specific: component + trigger) · Probability (H/M/L) · Impact (H/M/L) · Detected by · Recovery owner -->
<!-- Every entry must name a real component from [[05_COMPONENTS]] — no generic failures -->
{failure_mode_map}

## SINGLE POINTS OF FAILURE
<!-- Components whose failure breaks the entire system -->
<!-- [INF] if identified during inference loop with 05_COMPONENTS -->
<!-- Derived from MODEL REASONING Step 1 — must match SPOF in [[05_COMPONENTS]] -->
{single_points_of_failure}

## CASCADING FAILURES
<!-- If X fails, what else fails as a consequence? -->
<!-- Format: [Component X fails] → [Component Y cannot run] → [Component Z has no data] → [User impact] -->
<!-- At least 2 cascade levels required -->
{cascading_failures}

## RECOVERY PROTOCOLS
<!-- How to recover from each critical failure mode -->
<!-- Format: Failure · Recovery steps (ordered) · Owner (from [[09_AGENTS]]) · Estimated recovery time -->
<!-- Failures with no recovery path → flagged [UNOWNED-RECOVERY] and added to [[12_ASKED]] -->
{recovery_protocols}

## SILENT FAILURES
<!-- Failures that do not generate errors but corrupt outputs -->
<!-- Derived from MODEL REASONING Step 2 — at least one required -->
<!-- Format: Pattern name · Component involved · How it manifests · Detection method -->
{silent_failures}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 4 found an unowned recovery for a high-impact failure,
     or if SELF-INTERROGATION question 2 found zero silent failures (suspicious — confirm with user).
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 08_LIMITS
<!-- Pre-generation: asked to 08_LIMITS before this file was written -->
> "Given these failure modes, what must be explicitly prohibited
>  or constrained to prevent systemic failure?"
>
> Answer received: {inference_answer_from_08}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 05_COMPONENTS
<!-- Answer provided to 05_COMPONENTS during inference loop -->
> Question received: "Which component is the most likely single point of failure?
>  What breaks first when this component fails?"
> Answer given: {inference_answer_to_05}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[05_COMPONENTS]] for the components that can fail
→ see [[08_LIMITS]] for limits derived from these failure modes
→ see [[09_AGENTS]] for who handles recovery
→ see [[12_ASKED]] for unresolved failure scenarios
<!-- Add here: unowned recovery paths from MODEL REASONING Step 4 → [[12_ASKED]] -->
```
