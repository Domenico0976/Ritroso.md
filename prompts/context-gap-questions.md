# Context Gap Questions — Protocol

> This protocol is triggered when context is empty or insufficient.
> The model must NOT hallucinate. Instead, it asks one focused question at a time.

---

## Core Rule

**One question at a time. The most blocking one first.**

Never ask multiple questions in one turn. Never ask about aesthetics, style or preferences.
Only ask about dimensions that, if missing, prevent coherent generation.

---

## Answer Quality Threshold

After each user answer, evaluate it against this threshold **before** marking the gap RESOLVED.
An answer that fails the threshold is treated as a **partial answer** — ask a targeted follow-up.

| Gap type | Minimum viable answer | Insufficient answer (→ ask follow-up) |
|----------|-----------------------|---------------------------------------|
| `goal_gap` | Names a specific problem AND a specific affected person/role | Generic ("I want to build something useful", "for people") |
| `constraint_gap` | At least one concrete constraint with a value (time, money, team size, tech) | Vague ("limited budget", "some constraints", "no specific deadline") |
| `antipattern_gap` | Names one specific thing that must not happen or has failed before | Generic ("keep it simple", "don't over-engineer") |
| `user_gap` | Names at least one role/persona with a specific behaviour or need | Generic ("users", "people who need help") |
| `stack_gap` | Names at least one real technology, framework, or service | Vague ("standard tech", "whatever works best", "modern stack") |

### Insufficient Answer Protocol

When an answer is insufficient, do NOT mark the gap RESOLVED.
Instead, ask ONE targeted follow-up that makes the requirement explicit:

```
goal_gap insufficient example:
  User: "I want to build a productivity app"
  Follow-up: "Who specifically struggles with productivity in a way this app would fix?
  (e.g. 'freelance developers who lose track of client tasks', not just 'people')"

constraint_gap insufficient example:
  User: "limited budget"
  Follow-up: "Roughly how limited? (e.g. solo/free tier, <€100/month, <€1000 total)"

stack_gap insufficient example:
  User: "modern web stack"
  Follow-up: "Which specific framework or service are you using or considering?
  (e.g. Next.js, SvelteKit, plain HTML, WordPress, something else)"
```

Maximum **one follow-up per gap**. If the follow-up answer is still insufficient →
mark the gap as `PARTIAL` with `confidence: LOW` and carry it as `[OPEN]` into `12_ASKED.md`.

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
(A one-sentence answer is enough — but it must name the problem and the person.)
```

### constraint_gap
```
Before I can map limits and scope correctly:

What are the most important constraints on this project?
(e.g. budget cap in €/month, deadline in weeks, team size, platform lock-in, legal requirements)
```

### antipattern_gap
```
To avoid generating generic or wrong recommendations:

What must this project NOT do? What has failed in similar attempts?
(Even a rough answer helps — name one specific thing to avoid.)
```

### user_gap
```
To properly define agents, elements and flows:

Who are the main users or actors in this system?
(e.g. "solo developer managing 3 client projects", "team of 5 with one non-technical PM")
```

### stack_gap
```
To generate accurate components and infrastructure:

What is the technology stack or architecture of this project?
(e.g. Next.js + Supabase, Python + Docker, custom agent framework, WordPress)
```

---

## Answer Merge Protocol

After each user answer:

1. Evaluate against the **Answer Quality Threshold** above
2. If sufficient → merge into active context, mark gap `RESOLVED` with `confidence: HIGH`
3. If insufficient → ask one targeted follow-up (see Insufficient Answer Protocol above)
4. If follow-up is also insufficient → mark gap `PARTIAL` with `confidence: LOW` + `[OPEN]` flag
5. Re-evaluate remaining gaps in priority order
6. If minimum viable context reached → proceed to inference loop

### Minimum Viable Context

The inference loop can start when **at least** these gaps are resolved:
- `goal_gap` → RESOLVED (confidence: HIGH or MEDIUM — not LOW)
- `constraint_gap` → RESOLVED or PARTIAL with explicit `[OPEN]` flag

A `goal_gap` with confidence LOW blocks generation — ask again.
All other gaps can be carried as `[OPEN]` items into `12_ASKED.md`.

---

## Empty Prompt — Full Sequence Example

```
User: "build something"
→ gap detected: goal_gap, constraint_gap, antipattern_gap, user_gap, stack_gap
→ ask: goal_gap question

User: "a tool to manage my AI agents"
→ evaluate: names a tool + domain — but who are the users? Minimum viable: YES
→ goal_gap: RESOLVED (confidence: MEDIUM — user not specified, will appear as [OPEN])
→ remaining: constraint_gap, user_gap, stack_gap
→ ask: constraint_gap question

User: "limited budget"
→ evaluate: vague — no value, no timeframe
→ INSUFFICIENT → ask follow-up: "Roughly how limited? (e.g. solo/free tier, <€100/month)"

User: "solo project, 2 weeks, free tier only"
→ evaluate: time (2 weeks) + team (solo) + budget (free tier) — sufficient
→ constraint_gap: RESOLVED (confidence: HIGH)
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
    answer: "{{ specific user answer }}"
    confidence: HIGH | MEDIUM
    note: "{{ any relevant nuance or partial ambiguity }}"
  - gap_type: constraint_gap
    answer: "{{ specific user answer }}"
    confidence: HIGH | MEDIUM
    note: "{{ any relevant nuance }}"
open_gaps:
  - gap_type: user_gap
    status: "[OPEN] — will appear in 12_ASKED.md"
    confidence: LOW
    reason: "{{ why not resolvable from context }}"
  - gap_type: stack_gap
    status: "[OPEN] — will appear in 12_ASKED.md"
    confidence: LOW
    reason: "{{ why not resolvable from context }}"
```
