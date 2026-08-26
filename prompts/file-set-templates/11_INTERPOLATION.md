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

---

## MODEL REASONING
<!-- EXECUTE BEFORE WRITING ANY SECTION BELOW.
     This file is the connective tissue of the entire set. Reason carefully. -->

**Step 1 — Build a mental map of all named items across the file set.**
List every named element from [[04_ELEMENTS]], every component from [[05_COMPONENTS]], and every agent from [[09_AGENTS]].
Do not invent new items here — only use what is already named in other files.
If an item appears in one file but has no counterpart in another → it is a disconnected node. Flag as [OPEN-INF-N].

**Step 2 — Identify the critical path.**
Which sequence of connections must succeed for the primary user flow in [[02_PRODUCT]] to complete?
This is the critical path. Every connection on it is a high-risk connection.
Mark each with [CRITICAL-PATH] in CONNECTION MAP.

**Step 3 — Find hidden dependencies.**
For each external integration: what does the system assume about it that is not guaranteed?
(Examples: assumes 99.9% uptime, assumes response < 200ms, assumes auth token is always valid.)
Each hidden assumption is a latent risk. List them. They go into EXTERNAL INTEGRATIONS as notes.

**Step 4 — Map the cascade from the most likely failure.**
Using the critical failure point from [[10_ERROR]], trace what happens step by step when it fails.
Level 1: what breaks immediately. Level 2: what that blocks. Level 3: what reaches the user.
This cascade becomes the CRITICAL DEPENDENCY section.

**Step 5 — Verify the graph is complete.**
Every agent in [[09_AGENTS]] must appear at least once in the CONNECTION MAP.
Every external service in [[11_INTERPOLATION]] must have a fallback status (NONE/PARTIAL/COVERED).
If any agent or service is missing → add it or flag as [OPEN-INF-N].

<!-- Log any step that cannot be completed as [OPEN-INF-N]. -->

---

## SUB-AGENT QUERIES
<!-- Send these queries before writing sections. -->

**Query → context-gap agent:**
> "What connections between system components are implied by the product description
>  but have not been explicitly documented in any file?
>  List only connections whose absence would create a silent coordination failure."
> Answer: {subagent_context_gap_answer}

**Query → negative-verification agent:**
> "What does a connection map that looks complete but hides a systemic risk look like?
>  Name the two most common integration failure patterns for systems of this type."
> Answer: {subagent_negative_verification_answer}

**Query → 10_ERROR (failure cascade):**
> "What is the most critical failure mode identified in 10_ERROR?
>  Trace the full cascade: what breaks first, what fails next, what reaches the user."
> Answer: {subagent_error_cascade_answer}

**Query → 09_AGENTS (agent flow check):**
> "Which agent handoffs defined in 09_AGENTS are not yet reflected in this connection map?
>  Are there handoffs that depend on an external service that has no fallback?"
> Answer: {subagent_agents_flow_answer}

**Query → inference-loop agent (gap check):**
> "After the full inference loop, which cross-file signal was marked as a DEPENDENCY or RISK
>  and has not yet been represented as a connection in this file?"
> Answer: {subagent_inference_gap_answer}

<!-- All answers inject into sections below. Reference explicitly. -->

---

## SELF-INTERROGATION
<!-- Answer each before writing sections. -->

1. **Does every agent from [[09_AGENTS]] appear at least once in the CONNECTION MAP?** If not → disconnected agent. Flag.
2. **Is the CRITICAL PATH identified and marked?** If the critical path is not clear, the system is not understood well enough to build.
3. **Does SIMPLIFIED GRAPH include at least one failure path?** A graph with only happy paths is incomplete by definition.
4. **Are all external integrations listed with their failure behavior?** "Unknown" is not acceptable — estimate or flag as [OPEN-INF-N].
5. **Is there a bidirectional dependency that could create a deadlock?** (A depends on B to start, B depends on A to complete.) If yes → flag in CONNECTION MAP and add to [[12_ASKED]].

<!-- Flag unresolved as [OPEN-INF-N]. -->

---

## CONNECTION MAP
<!-- REQUIRED FORMAT — one entry per significant connection:
     {Source} → {Target} · {type: data/control/event/dependency} · {direction: uni/bidirectional} · {condition: always/on-trigger/on-failure}
     [CRITICAL-PATH] tag for connections on the critical path from MODEL REASONING Step 2.
     Do NOT list isolated components — only named connections between named items from 04_ELEMENTS and 05_COMPONENTS.
     Every agent from [[09_AGENTS]] must appear at least once. -->
{connection_map}

## CRITICAL DEPENDENCY
<!-- The single most critical dependency — [INF] required if from inference loop -->
<!-- Derived from MODEL REASONING Step 4 cascade map -->
<!-- REQUIRED FORMAT:
     Dependency: {real service, module, or role}
     Type: {external service / internal module / human role / API contract}
     Cascade if broken:
       Level 1: {what fails immediately}
       Level 2: {what that blocks}
       Level 3: {what reaches the user / the goal}
     Mitigation: {NONE | PARTIAL — describe | COVERED — describe mechanism}
     Detection: {how is degradation observable before Level 3 is reached} -->
{critical_dependency}

## DATA FLOWS
<!-- How data moves through the system — each flow must be complete:
     {input source} → {transform/processor} → {output destination}
     Include: data type, volume (rough), failure behavior if transform fails -->
{data_flows}

## EXTERNAL INTEGRATIONS
<!-- Third-party APIs, services, webhooks, SDKs —
     | Integration | Type | Auth method | Rate limit/quota | Failure behavior | Fallback? |
     Hidden assumptions from MODEL REASONING Step 3 noted inline.
     If no external integrations: N/A — [reason] (do not leave blank) -->
{external_integrations}

## EVENTS / TRIGGERS
<!-- What triggers what — REQUIRED FORMAT:
     {Actor/Event} → triggers → {Action/Component} → produces → {Output/State change}
     Include at minimum: primary user action, primary error event, primary scheduled event -->
{events_triggers}

## SIMPLIFIED GRAPH
<!-- Text-based directed graph — MUST include at least one failure path (from SELF-INTERROGATION question 3):

     [Entry point]
       → [Core process A]
           → [Component X] → [Output 1]
           ✗ [Component X fails] → [Error handler] → [Recovery path]
       → [Core process B]
           ↔ [External service]

     [CRITICAL-PATH] nodes are marked.
     Disconnected nodes from MODEL REASONING Step 1 appear as: [DISCONNECTED: node name] -->
{simplified_graph}

## BLOCKING QUESTIONS
<!-- Ask the user ONLY if SELF-INTERROGATION question 5 found a deadlock risk,
     or if a critical external integration has no fallback and no workaround.
     Stop generation and wait before continuing. -->
{blocking_questions_to_user}

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
<!-- Add here: any deadlock risk from SELF-INTERROGATION question 5 -->
```
