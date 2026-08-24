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

## HARD LIMITS
<!-- Absolute constraints — violating these breaks the system or the project -->
<!-- REQUIRED FORMAT — one row per limit:
     | Limit (specific action/value prohibited) | Category (Technical/Financial/Legal/Scope/Performance) | Source (which failure mode in 10_ERROR or constraint declared by user) | Consequence if violated (specific, not "bad things happen") |
     Minimum 3 hard limits. If fewer exist, explain why in a note. -->
{hard_limits}

## SOFT LIMITS
<!-- Boundaries that should not be crossed but have workarounds -->
<!-- Same format as HARD LIMITS but with an additional "Workaround" column -->
<!-- Minimum 2 soft limits. -->
{soft_limits}

## ANTI-PATTERNS
<!-- Approaches that have failed or must be avoided for THIS project -->
<!-- REQUIRED: each anti-pattern must be specific to this domain/stack — no generic "avoid over-engineering" -->
<!-- [INF] if derived from failure analysis in inference loop -->
<!-- Format:
     - Anti-pattern name: {specific pattern, e.g. "polling every 500ms for real-time updates"}
       Why it fails here: {reason tied to this project's constraints}
       Alternative: {what to do instead} -->
{anti_patterns}

## REGULATORY / LEGAL
<!-- Compliance requirements applicable to THIS project's domain and data -->
<!-- If none apply: N/A — [reason: domain, data type, jurisdiction] -->
<!-- Do NOT leave blank — a blank section means the question was not asked -->
{regulatory_limits}

## SCOPE LIMITS
<!-- What is explicitly OUT OF SCOPE for this version — not "future work" generically -->
<!-- Each item must be specific and tied to a constraint (budget, time, team, dependency) -->
<!-- Format:
     - OUT: {feature/capability}
       Reason: {specific constraint that excludes it}
       Revisit when: {condition that would change this — or "never in this version"} -->
{scope_limits}

## LIMIT OWNERSHIP MAP
<!-- MANDATORY: each limit must have an owner in 09_AGENTS -->
<!-- Format:
     | Limit | Owner (from 09_AGENTS agent map) | Monitoring method | Escalation trigger |
     Unowned limits are flagged as [OPEN] and must appear in 12_ASKED.md -->
{limit_ownership_map}

## INFERENCE QUESTION → 09_AGENTS
<!-- Pre-generation: asked to 09_AGENTS before this file was written -->
> "Who is responsible for monitoring these limits and responding to failures?
>  Is there an agent (AI or human) capable of doing this with current resources?"
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
```
