# Template: 11_INTERPOLATION.md

> Answers: HOW does everything connect?
> Inference loop: receives question from 03_NEXT_STEPS, asks 12_ASKED.

---

```markdown
---
name: "11_INTERPOLATION.md"
description: "Use this file to interpret the project as a connected system of dependencies, signals, agents, components, and flows. Read relationships through critical-path thinking: identify what carries the most coordination risk, what breaks downstream when a dependency fails, and what external integrations constrain the design. Prefer connection maps that expose systemic pressure instead of hiding it behind isolated feature descriptions."
---

# 11_INTERPOLATION.md — {project_name}
{standard_header}

## CONNECTION MAP
<!-- REQUIRED FORMAT — one entry per significant connection:
     {Source} → {Target} · {connection_type: data/control/event/dependency} · {direction: unidirectional/bidirectional} · {condition: always/on-trigger/on-failure}
     Do NOT list isolated components — only named connections between named items from 04_ELEMENTS and 05_COMPONENTS -->
{connection_map}

## CRITICAL DEPENDENCY
<!-- The single most critical dependency — [INF] required if from inference loop -->
<!-- REQUIRED FORMAT:
     Dependency: {name — real service, module, or role}
     Type: {external service / internal module / human role / API contract}
     Cascade if broken:
       Level 1: {what fails immediately}
       Level 2: {what that blocks}
       Level 3: {what reaches the user / the goal}
     Mitigation: {NONE | PARTIAL — describe what exists | COVERED — describe mechanism}
     Detection: {how is degradation observable before Level 3 is reached} -->
{critical_dependency}

## DATA FLOWS
<!-- How data moves through the system — each flow must be complete:
     {input source} → {transform/processor} → {output destination}
     Include: data type, volume (rough), failure behavior if transform fails -->
{data_flows}

## EXTERNAL INTEGRATIONS
<!-- Third-party APIs, services, webhooks, SDKs —
     | Integration | Type | Auth method | Rate limit / quota | Failure behavior | Fallback? |
     If no external integrations: N/A — [reason] (do not leave blank) -->
{external_integrations}

## EVENTS / TRIGGERS
<!-- What triggers what — REQUIRED FORMAT:
     {Actor/Event} → triggers → {Action/Component} → produces → {Output/State change}
     Include at minimum: primary user action, primary error event, primary scheduled event -->
{events_triggers}

## SIMPLIFIED GRAPH
<!-- Text-based directed graph of the project's core flows —
     Use indentation to show hierarchy, → for dependencies, ↔ for bidirectional, ✗ for failure paths:

     [Entry point]
       → [Core process A]
           → [Component X] → [Output 1]
           ✗ [Component X fails] → [Error handler] → [Recovery path]
       → [Core process B]
           ↔ [External service]

     Must include at least one failure path -->
{simplified_graph}

## INFERENCE QUESTION → 12_ASKED
<!-- Pre-generation: asked to 12_ASKED before this file was written -->
> "After this entire inference chain, what is the most important question
>  that still has no answer and would most change the design if answered?"
>
> Answer received: {inference_answer_from_12}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## INFERENCE ANSWER ← 03_NEXT_STEPS
<!-- Answer provided to 03_NEXT_STEPS during inference loop -->
> Question received: "What is the most critical dependency in the system?
>  If this dependency breaks, what cascades?"
> Answer given: {inference_answer_to_03}
> Answer quality: [PASS | PARTIAL | OPEN-INF-N]

## DEPENDENCIES
→ see [[04_ELEMENTS]] for connected elements
→ see [[09_AGENTS]] for agent interaction flows
→ see [[10_ERROR]] for failure modes in connections
→ see [[05_COMPONENTS]] for component interfaces
```
