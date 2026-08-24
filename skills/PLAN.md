---
name: "PLAN.md"
version: "1.0"
description: "Mandatory execution protocol for the Ritroso skill. Defines the generation timeline, project rules extracted from all file types, gate conditions that block progression, and the Panel of Agents that attacks every decision before a file is closed."
applies_to: "All Ritroso file-set generations"
---

# PLAN.md — Ritroso Execution Protocol

> This file is not optional. Every Ritroso generation MUST follow this protocol in order.
> Skipping a phase or a gate is a protocol violation. When in doubt, stop and ask.

---

## PHASE 0 — PROMPT INTAKE & CLASSIFICATION

### Step 0.1 — Read the prompt exactly as given. Do not improve it.
The raw prompt is the ground truth. Do not infer what the user "probably meant".
Do not upgrade vocabulary, add technical terms, or assume domain expertise.

### Step 0.2 — Classify the project domain
Assign one primary domain from this list:
- `software-product` — app, SaaS, tool, API
- `creative-technical` — audiovisual, generative art, interactive installation
- `content-operations` — editorial, CMS, workflow, publishing
- `open-source-framework` — library, skill, prompting framework, dev tool
- `service-or-agency` — consulting deliverable, client project
- `other` — document it explicitly

### Step 0.3 — Count structural ambiguities
A structural ambiguity is any gap that changes architecture, pricing, scope, or distribution.
Count them. If count ≥ 2 → GATE 0 is triggered.

### GATE 0 — Pre-generation block
**Condition**: If structural ambiguities ≥ 2, DO NOT generate any file.
Instead: ask the single most blocking question (not a list — one question only).
Wait for the answer. Then re-run Phase 0.
Only proceed to Phase 1 when structural ambiguities < 2.

> Rule: One question at a time. Never dump a list of questions on the user.
> The most blocking question is the one whose answer changes the most other answers.

---

## PHASE 1 — INFERENCE LOOP (pre-generation)

> This phase happens entirely before writing any file.
> The model interrogates its own understanding across all 13 file slots.

### Step 1.1 — Run the inter-file inference questions
For each file slot, ask internally:

| File | Inference Question |
|------|--------------------|
| 01_GOAL | Why does this project exist? What problem does it solve for a specific person? |
| 02_PRODUCT | What does it concretely do? Can I describe the user flow in 3 steps? |
| 03_NEXT_STEPS | What is the smallest thing that proves the concept works? |
| 04_ELEMENTS | What must exist for any of this to function? What is not yet named? |
| 05_COMPONENTS | How is it built? Is the stack consistent with 07_BUDGET and 08_LIMITS? |
| 06_PRICE | What does the user pay? Is this consistent with 01_GOAL? |
| 07_BUDGET | What does it cost to build? Is this consistent with 05_COMPONENTS? |
| 08_LIMITS | What cannot be done? Are any limits in conflict with 01_GOAL or 02_PRODUCT? |
| 09_AGENTS | Who decides when there is a trade-off? Name the decision owner explicitly. |
| 10_ERROR | What is the most likely failure? What is the worst-case failure? |
| 11_INTERPOLATION | Do all files point in the same direction? List every cross-file dependency. |
| 12_ASKED | What do we not know? Mark each as INFERRED or ASSUMED (see rules below). |

### Step 1.2 — Mark all assumptions
Every assumption in 12_ASKED must carry one of two tags:

- `[INFERRED-FROM-TEXT]` — the assumption is supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — the assumption fills a gap with no textual support

Rule: ASSUMED-NO-BASIS items are higher risk. Flag them in 10_ERROR as well.

### Step 1.3 — Check goal compatibility
Before generating any file, explicitly ask:
> "Are the objectives in 01_GOAL mutually compatible?"
> "Does achieving Goal A make Goal B harder or impossible?"

If a conflict is found: it is NOT a limit (08_LIMITS). It is a contradiction and must be flagged in 11_INTERPOLATION as `[GOAL-CONFLICT]` with explicit description.

---

## PHASE 2 — PANEL OF AGENTS

> Every file passes through all 4 agents before being closed.
> If any agent raises a BLOCK, the file is regenerated — not annotated.
> A NOTE is a suggestion. A BLOCK is mandatory regeneration.

### The 4 Agents

---

#### 🏛 ARCHITECT
**Question**: "Does this decision hold if the project doubles in scope, changes platform, or changes target user?"

Rules:
- BLOCK if a component is tightly coupled to a specific tool that is also marked as a soft limit
- BLOCK if the architecture in 05_COMPONENTS contradicts 08_LIMITS
- BLOCK if 03_NEXT_STEPS P1 includes anything that requires P2 infrastructure to work
- NOTE if the stack has no clear upgrade path for P3 features
- NOTE if a single point of failure exists with no fallback in 10_ERROR

---

#### 🎨 DESIGNER
**Question**: "Can a person who did not write this prompt read this file and know exactly what to do next?"

Rules:
- BLOCK if 03_NEXT_STEPS contains a step with no concrete output (e.g. "think about X" or "research Y" without a deliverable)
- BLOCK if 02_PRODUCT describes the product without a user flow (minimum: 3-step flow)
- BLOCK if 09_AGENTS lists a role with no concrete responsibility (e.g. "helps with X" is not a responsibility)
- NOTE if any file uses jargon that is not defined elsewhere in the file set
- NOTE if 12_ASKED open questions are listed without stating what decision they block

---

#### ⚙️ PRAGMATIST
**Question**: "Is this actually buildable with the time, money, and people declared in 07_BUDGET and 09_AGENTS?"

Rules:
- BLOCK if 03_NEXT_STEPS P1 scope exceeds the P1 hours in 07_BUDGET by more than 50%
- BLOCK if a component in 05_COMPONENTS requires expertise not represented in 09_AGENTS
- BLOCK if 06_PRICE and 07_BUDGET are inconsistent (e.g. free product but paid infrastructure)
- BLOCK if any P1 step depends on a decision marked OPEN in 12_ASKED
- NOTE if the total hour estimate in 07_BUDGET has no range (single number = false precision)

---

#### ⚠️ RISK
**Question**: "What is the single most likely way this project fails in the first 30 days?"

Rules:
- BLOCK if 10_ERROR does not include at least one process/human failure mode (not just technical)
- BLOCK if an `[ASSUMED-NO-BASIS]` item in 12_ASKED has no corresponding risk in 10_ERROR
- BLOCK if the negative verification has not been run (see Phase 3)
- BLOCK if 08_LIMITS contains a hard limit that is violated by any step in 03_NEXT_STEPS
- NOTE if all risks in 10_ERROR are marked Medium or Low (no High = likely underestimating)
- NOTE if 09_AGENTS has no named decision owner for the highest-impact trade-off in the project

---

## PHASE 3 — NEGATIVE VERIFICATION

> This runs AFTER all 13 files are drafted, BEFORE they are closed.
> The model must argue against its own output.

### Step 3.1 — Goal attack
For each goal in 01_GOAL, ask:
> "What is the strongest argument that this goal is wrong, incomplete, or self-contradictory?"
Document findings in 11_INTERPOLATION under `## Negative Verification — Goal Attack`.

### Step 3.2 — Architecture attack
For 05_COMPONENTS, ask:
> "What is the most likely reason this architecture fails in production?"
> "What assumption does this architecture make that is not stated in 08_LIMITS?"
Document in 10_ERROR under `## Negative Verification — Architecture Attack`.

### Step 3.3 — Roadmap attack
For 03_NEXT_STEPS, ask:
> "Which step is most likely to be skipped under time pressure?"
> "Which step, if skipped, causes the most downstream damage?"
Add a `## Skippability Risk` section to 03_NEXT_STEPS with this analysis.

### Step 3.4 — Assumption attack
For every `[ASSUMED-NO-BASIS]` in 12_ASKED, ask:
> "If this assumption is wrong, which files need to be rewritten?"
List the affected files explicitly next to each assumption.

### GATE 3 — Negative verification sign-off
**Condition**: If Phase 3 produces any new BLOCK-level finding, the affected file(s) must be regenerated before the file set is closed.
The file set is only marked `RITROSO-VERIFIED` after Gate 3 is passed with no open BLOCKs.

---

## PHASE 4 — FILE CLOSURE & INDEX

### Step 4.1 — Write 00_INDEX.md last
00_INDEX is written only after all other 12 files are closed and verified.
It must reflect the actual verification status, not a template.

### Step 4.2 — Verification status block
00_INDEX must contain:
```
## Verification Status
- Inference loop: COMPLETE / INCOMPLETE
- Panel of Agents: PASSED / BLOCKS REMAINING (list them)
- Negative verification: PASSED / OPEN ATTACKS (list them)
- Open questions: N (list OPEN IDs)
- Goal conflicts: N (list [GOAL-CONFLICT] IDs or NONE)
- ASSUMED-NO-BASIS items: N (list or NONE)
```

### Step 4.3 — Do not mark RITROSO-VERIFIED if any of the following are true:
- Any BLOCK from the Panel of Agents is unresolved
- Any Phase 3 attack produced a finding that was not addressed
- Any P1 step in 03_NEXT_STEPS depends on an unresolved OPEN item
- 11_INTERPOLATION contains a [GOAL-CONFLICT] that was not discussed with the user

---

## PROJECT RULES (extracted from file types — mandatory for all generations)

These rules are derived from patterns across all Ritroso file types. They are not suggestions.

### Rules from 01_GOAL
- R-GOAL-1: Every goal must name a specific person or role who benefits. "Improve X" without a beneficiary is not a goal.
- R-GOAL-2: Anti-goals are mandatory. At least one anti-goal must be stated.
- R-GOAL-3: If two goals are present, they must be checked for mutual compatibility before any other file is written.

### Rules from 02_PRODUCT
- R-PROD-1: MVP must be definable in one sentence and one 3-step user flow.
- R-PROD-2: P1, P2, P3 must be explicitly separated. Features without a phase assignment do not exist.
- R-PROD-3: "Future features" without a phase label are banned — they create false scope.

### Rules from 03_NEXT_STEPS
- R-STEPS-1: Every step must have a concrete output. "Research X" is only valid if it produces a named deliverable.
- R-STEPS-2: No P1 step may depend on infrastructure, data, or decisions from P2 or P3.
- R-STEPS-3: The skippability risk section is mandatory (added by Phase 3).

### Rules from 04_ELEMENTS
- R-ELEM-1: Every element must be classified as Critical (P1), Important (P2), or Optional (P3).
- R-ELEM-2: The Minimum Viable Set must be explicitly listed — not inferred from the table.

### Rules from 05_COMPONENTS
- R-COMP-1: Stack must be cross-checked against 07_BUDGET (can we afford this?) and 08_LIMITS (is it allowed?).
- R-COMP-2: Every component must appear in 04_ELEMENTS. A component not in Elements does not exist.
- R-COMP-3: The architecture must include a node pipeline diagram showing data/signal flow.

### Rules from 06_PRICE
- R-PRICE-1: If price is TBD, a decision deadline must be stated (e.g. "must be decided before P2").
- R-PRICE-2: Price model must be consistent with 01_GOAL. A free tool with paid infrastructure is a contradiction.

### Rules from 07_BUDGET
- R-BUDG-1: All time estimates must be ranges (e.g. 16-24h). Single numbers are false precision.
- R-BUDG-2: Variable costs (e.g. AI API, cloud compute) must be explicitly separated from fixed costs.
- R-BUDG-3: A "budget reality check" question must appear: "Can this be built within these constraints?"

### Rules from 08_LIMITS
- R-LIM-1: Every hard limit must state the reason, not just the constraint.
- R-LIM-2: Anti-patterns are mandatory (at least 3). An anti-pattern is a specific action that must never be taken.
- R-LIM-3: If a limit conflicts with a goal, it must be flagged as [GOAL-CONFLICT] in 11_INTERPOLATION.

### Rules from 09_AGENTS
- R-AGNT-1: Every agent must have at least one named decision point — not just a responsibility.
- R-AGNT-2: When a trade-off exists between cost, time, and quality, the file must name which agent decides and by what rule.
- R-AGNT-3: Handoff points between agents must be explicit: what is handed off, in what format, and when.

### Rules from 10_ERROR
- R-ERR-1: At least one process/human failure mode must appear (not just technical risks).
- R-ERR-2: Every `[ASSUMED-NO-BASIS]` item from 12_ASKED must have a corresponding risk entry.
- R-ERR-3: At least one risk must be marked High probability or High impact. No all-Medium risk lists.
- R-ERR-4: Every risk must have both Probability and Impact labeled, not just a mitigation.

### Rules from 11_INTERPOLATION
- R-INTERP-1: The dependency chain must be a causal chain (A → causes → B), not a list of relationships.
- R-INTERP-2: Every [GOAL-CONFLICT] must be documented with: what conflicts, why it conflicts, and what the resolution options are.
- R-INTERP-3: The negative verification findings section is mandatory (added by Phase 3).

### Rules from 12_ASKED
- R-ASKED-1: Every open question must state which file(s) it blocks if unanswered.
- R-ASKED-2: Every assumption must carry either `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]`.
- R-ASKED-3: No open question may remain without a safest assumption — but the assumption must be tagged and justified.
- R-ASKED-4: Questions are sorted by blocking priority: structural blockers first, optimisation questions last.

---

## QUICK REFERENCE — GATE SUMMARY

| Gate | Trigger | Action |
|------|---------|--------|
| GATE 0 | Structural ambiguities ≥ 2 | Ask one blocking question. Do not generate. |
| GATE 1 | Goal conflict found in Step 1.3 | Flag as [GOAL-CONFLICT]. Do not proceed until documented. |
| GATE 2 | Panel of Agents raises a BLOCK | Regenerate the affected file. Do not annotate. |
| GATE 3 | Negative verification finds new BLOCK | Regenerate affected files. Do not close file set. |
| GATE 4 | Any P1 step depends on unresolved OPEN | Remove from P1 or resolve the OPEN item first. |

---

## QUICK REFERENCE — AGENT BLOCK TRIGGERS

| Agent | Triggers a BLOCK when... |
|-------|-------------------------|
| ARCHITECT | Component contradicts limits; P1 needs P2 infrastructure; architecture incompatible with stack |
| DESIGNER | Step has no output; product has no user flow; agent role is vague |
| PRAGMATIST | P1 scope exceeds budget by >50%; missing expertise; price/budget inconsistency; P1 step needs unresolved OPEN |
| RISK | No human failure mode; ASSUMED item has no risk entry; hard limit violated in roadmap; no High risk in 10_ERROR |

---

## VERSION HISTORY
| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-08-24 | Initial release — timeline, project rules, panel of agents |
