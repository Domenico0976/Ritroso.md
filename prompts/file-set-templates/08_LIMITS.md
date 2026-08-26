# Template: 08_LIMITS.md

> Answers: WHAT cannot / must not be done?
> Inference loop: receives question from 10_ERROR, asks 09_AGENTS.

---

```markdown
---
name: "08_LIMITS.md"
description: "Use this file to reason about what the project must not violate if it wants to remain coherent, safe, and buildable. Interpret every limit as an active design boundary informed by failures, constraints, budget, legal obligations, and scope compression. Prefer hard clarity over optional flexibility, and treat inferred anti-patterns as operational warnings that should shape every downstream decision."
---

# 08_LIMITS.md — {project_name}
{standard_header}

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW. -->

**Step 1 — Derive limits from failure modes, not from intuition.**
For each failure mode in [[10_ERROR]] FAILURE MODE MAP with impact H:
Name the limit that, if enforced, would prevent or contain that failure.
Format: Failure "X" → limit "Y must not exceed Z" or "action A is prohibited".
Do not invent limits that no failure mode requires.

**Step 2 — Separate hard from soft limits.**
Hard limit: violating it causes irreversible damage (data loss, security breach, contract violation, total outage).
Soft limit: violating it degrades quality, increases cost, or creates risk but is recoverable.
If you are unsure whether a limit is hard or soft → it is hard. Err toward strictness.

**Step 3 — Check for legal and regulatory surface.**
What data does this product handle? (user PII, financial, health, children's data, EU users, etc.)
For each data type: what regulation applies (GDPR, CCPA, HIPAA, PCI-DSS, COPPA, etc.)?
If no regulated data: state explicitly — do not leave the REGULATORY/LEGAL section blank.

**Step 4 — Map every limit to an owner.**
For each limit identified: is there an agent in [[09_AGENTS]] who owns monitoring and enforcement?
If no agent owns a limit → it is an unenforceable limit. This is a design failure. Flag as [OPEN].
An unowned hard limit with no agent is a CRITICAL gap → add to [[12_ASKED]] as blocking.

**Step 5 — Find the anti-patterns specific to this stack and domain.**
Given the architecture in [[05_COMPONENTS]] and the failure modes in [[10_ERROR]]:
Name 2-3 implementation patterns that would re-introduce the most dangerous failure modes.
These are project-specific anti-patterns, not generic best practices.

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send before writing sections. -->

**Query → context-gap agent:**
> "What constraints are implied by the product's domain, data types, or user base
>  that have not been explicitly stated as limits?
>  List only constraints whose absence would create a compliance or safety gap."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a limit set that looks complete but fails to prevent the most dangerous failure look like?
>  Name the two most common limit design failures for systems of this type."
> Answer: {subagent_negative_verification_answer}

**Query → 10_ERROR (failure-to-limit mapping):**
> "For each high-impact failure mode in 10_ERROR: what specific limit would prevent or contain it?
>  Are there failure modes that have no corresponding limit defined here?"
> Answer: {subagent_error_mapping_answer}

**Query → 09_AGENTS (ownership check):**
> "Who is responsible for monitoring these limits and responding to violations?
>  Is there any limit that no agent currently owns?"
> Answer: {subagent_agents_ownership_answer}

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every HARD LIMIT trace back to a failure mode in [[10_ERROR]]?** A limit with no failure source is either orphaned or based on an assumption not documented. Flag it.
2. **Is there a limit that can be violated without anyone noticing?** If yes → it is unenforceable without a monitoring mechanism. Flag in LIMIT OWNERSHIP MAP.
3. **Is the REGULATORY/LEGAL section based on the actual data types handled?** Not on guessing. If data types are not confirmed → flag as [OPEN-INF-N].
4. **Does every limit in LIMIT OWNERSHIP MAP have a named agent?** Unowned limits are [OPEN] by definition.
5. **Are the ANTI-PATTERNS specific to this project's stack and domain?** Generic anti-patterns ("don't use globals") are noise. Replace them with specific patterns tied to this architecture.

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## HARD LIMITS
<!-- Absolute constraints — violating these breaks the system or the project -->
<!-- REQUIRED FORMAT:
     | Limit (specific action/value prohibited) | Category | Source (failure mode in [[10_ERROR]]) | Consequence if violated |
     Derived from MODEL REASONING Step 1. Minimum 3 hard limits. -->
{hard_limits}

## SOFT LIMITS
<!-- Boundaries with workarounds -->
<!-- Same format as HARD LIMITS + Workaround column -->
<!-- Derived from MODEL REASONING Step 2. Minimum 2 soft limits. -->
{soft_limits}

## ANTI-PATTERNS
<!-- Approaches that have failed or must be avoided for THIS project -->
<!-- [INF] if derived from failure analysis -->
<!-- Derived from MODEL REASONING Step 5 — must be stack/domain-specific -->
<!-- Format:
     - Anti-pattern: {specific pattern, e.g. "polling every 500ms for real-time updates"}
       Why it fails here: {reason tied to this project's constraints}
       Alternative: {what to do instead} -->
{anti_patterns}

## REGULATORY / LEGAL
<!-- Compliance requirements for THIS project's domain and data -->
<!-- Derived from MODEL REASONING Step 3 —  must not be blank -->
<!-- If none apply: N/A — [specific reason: domain, data type, jurisdiction] -->
{regulatory_limits}

## SCOPE LIMITS
<!-- What is explicitly OUT OF SCOPE for this version -->
<!-- Each item: feature/capability · constraint that excludes it · condition to revisit -->
{scope_limits}

## LIMIT OWNERSHIP MAP
<!-- MANDATORY — each limit must have an owner in [[09_AGENTS]] -->
<!-- Format:
     | Limit | Owner | Monitoring method | Escalation trigger |
     Unowned limits → [OPEN] → flagged in [[12_ASKED]] (SELF-INTERROGATION question 4) -->
{limit_ownership_map}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if MODEL REASONING Step 4 found an unowned hard limit,
     or if SELF-INTERROGATION question 3 confirmed data types are unknown.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

## INFERENCE QUESTION → 09_AGENTS
<!-- Pre-generation: asked to 09_AGENTS before this file was written -->
> "Who is responsible for monitoring these limits and responding to failures?
>  Is there an agent capable of doing this with current resources?"
>
> Answer received: {inference_answer_from_09}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 10_ERROR
<!-- Answer provided to 10_ERROR during inference loop -->
> Question received: "Given these failure modes, what must be explicitly prohibited
>  or constrained to prevent systemic failure?"
> Answer given: {inference_answer_to_10}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[10_ERROR]] for failure modes that drive these limits
→ see [[09_AGENTS]] for who enforces these limits
→ see [[03_NEXT_STEPS]] for limit-related actions
→ see [[12_ASKED]] for unresolved limit questions
<!-- Add here: any unenforceable limit from SELF-INTERROGATION question 2 → [[12_ASKED]] -->
```
