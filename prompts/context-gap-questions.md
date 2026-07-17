# Context Gap Questions — Protocol

> This protocol is triggered when context is empty or insufficient.
> The model must NOT hallucinate. Instead, it asks one focused question at a time.

---

## Core Rule

**One question at a time. The most blocking one first.**

Never ask multiple questions in one turn. Never ask about aesthetics, style or preferences.
Only ask about dimensions that, if missing, prevent coherent generation.

---

## Gap Priority Order

When multiple gaps exist, ask about them in this order:

| Priority | Gap Type | Ask About |
|----------|----------|-----------|
| 1 | `goal_gap` | Why does this project exist? What problem does it solve? |
| 2 | `constraint_gap` | What are the technical, economic, or time limits? |
| 3 | `antipattern_gap` | What must NOT be done? What has failed before? |
| 4 | `user_gap` | Who uses this? What are their main needs? |
| 5 | `stack_gap` | What technology / architecture is being used? |

---

## Question Templates by Gap Type

### goal_gap
```
To generate the complete file set I need to understand
the core reason this project exists:

What specific problem does this solve, and for whom?
(A one-sentence answer is enough.)
```

### constraint_gap
```
Before I can map limits and scope correctly:

What are the most important constraints on this project?
(e.g. budget, deadline, technical limits, team size, regulatory)
```

### antipattern_gap
```
To avoid generating generic or wrong recommendations:

What must this project NOT do? What has failed in similar attempts?
(Even a rough answer helps significantly.)
```

### user_gap
```
To properly define agents, elements and flows:

Who are the main users or actors in this system?
(e.g. roles, personas, technical level, usage frequency)
```

### stack_gap
```
To generate accurate components and infrastructure:

What is the technology stack or architecture of this project?
(e.g. Next.js + Supabase, Python + Docker, custom agent framework)
```

---

## Answer Merge Protocol

After each user answer:

1. Merge the answer into the active context
2. Mark the corresponding gap as `RESOLVED`
3. Re-evaluate remaining gaps
4. If more gaps → ask next question (priority order)
5. If minimum viable context reached → proceed to inference loop

### Minimum Viable Context

The inference loop can start when **at least** these gaps are resolved:
- `goal_gap` → RESOLVED
- `constraint_gap` → RESOLVED or INFERRED with [OPEN] flag

All other gaps can be carried as `[OPEN]` items into `12_ASKED.md`.

---

## Empty Prompt — Full Sequence Example

```
User: "build something"

→ gap detected: goal_gap, constraint_gap, antipattern_gap, user_gap, stack_gap
→ ask: goal_gap question

User: "a tool to manage my AI agents"
→ goal_gap: RESOLVED
→ remaining: constraint_gap, user_gap, stack_gap
→ ask: constraint_gap question

User: "solo project, 2 weeks, no budget"
→ constraint_gap: RESOLVED
→ remaining: user_gap, stack_gap (both can become [OPEN])
→ minimum viable context: REACHED
→ proceed to inference loop
```

---

## Inference Handoff

When transitioning to the inference loop, pass:

```yaml
resolved_gaps:
  - gap_type: goal_gap
    answer: "{{ user_answer }}"
    confidence: HIGH
  - gap_type: constraint_gap
    answer: "{{ user_answer }}"
    confidence: HIGH
open_gaps:
  - gap_type: user_gap
    status: "[OPEN] — will appear in 12_ASKED.md"
  - gap_type: stack_gap
    status: "[OPEN] — will appear in 12_ASKED.md"
```
