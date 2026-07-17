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
<!-- Format: Limit · Category · Reason · Consequence if violated -->
{hard_limits}

## SOFT LIMITS
<!-- Boundaries that should not be crossed but have workarounds -->
{soft_limits}

## ANTI-PATTERNS
<!-- Approaches that have failed or must be avoided -->
<!-- [INF] if derived from failure analysis in inference loop -->
{anti_patterns}

## REGULATORY / LEGAL
<!-- Compliance requirements, GDPR, licenses, contracts -->
{regulatory_limits}

## SCOPE LIMITS
<!-- What is out of scope for THIS version -->
{scope_limits}

## INFERENCE QUESTION → 09_AGENTS
<!-- Pre-generation: asked to 09_AGENTS before this file was written -->
> "Who is responsible for monitoring these limits and responding to failures?
>  Is there an agent (AI or human) capable of doing this with current resources?"
>
> Answer received: {inference_answer_from_09}

## INFERENCE ANSWER ← 10_ERROR
<!-- Answer provided to 10_ERROR during inference loop -->
> Question received: "Given these failure modes, what must be explicitly prohibited
>  or constrained to prevent systemic failure?"
> Answer given: {inference_answer_to_10}

## DEPENDENCIES
→ see [[10_ERROR]] for failure modes that drive these limits
→ see [[09_AGENTS]] for who enforces these limits
→ see [[03_NEXT_STEPS]] for limit-related actions
→ see [[12_ASKED]] for unresolved limit questions
```
