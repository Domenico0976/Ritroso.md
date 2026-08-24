---
name: "RITROSO.md"
version: "5"
description: "Ritroso execution skill v5. Defines what the 13 output files contain, how the Panel of Agents attacks each draft, how agents collaborate and escalate, and what the full template structure looks like for each file. PLAN.md defines when and how; this file defines what. Do not execute without PLAN.md loaded."
applies_to: "All Ritroso file-set generations"
---

# RITROSO — Contextual Reasoning Skill v5

> Contextual reasoning skill for LLMs with multimodal generation
> of complete `.md` file sets, inference loop, panel-of-agents validation,
> cross-agent attack protocols, and retroactive self-verification.

---

## ⚠️ MANDATORY: Read PLAN.md before executing anything

Before running any step below, load and follow `skills/PLAN.md`.
PLAN.md is the execution protocol. This file (RITROSO.md) is the skill definition.
They are not interchangeable. PLAN.md defines **when** and **how**. RITROSO.md defines **what**.

> If PLAN.md is not available in context: stop and ask the user to provide it before continuing.

---

## Execution Order

**When you receive a prompt, execute these steps in order. Do not skip or reorder.**

### STEP 1 — PLAN.md Phase 0 (Prompt Intake, Classification & Skill Discovery)
Before generating anything:
- Read the prompt exactly as written — do not upgrade vocabulary, do not infer intent
- Classify the project domain (see PLAN.md §0.2)
- Run Skill Discovery Engine (PLAN.md §0.4.0) — all 5 methods in priority order
- Count structural ambiguities (PLAN.md §0.3)
- If structural ambiguities ≥ 2 → GATE 0: ask one blocking question, stop
- Only proceed when structural ambiguities < 2

### STEP 2 — PLAN.md Phase 1 (Inference Loop)
Run all inter-file inference questions **internally** before writing any file.
- Mark every assumption as `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]`
- Check goal compatibility: flag any `[GOAL-CONFLICT]` before proceeding
- If a goal conflict is found → GATE 1: document it, do not proceed silently
- Active skill rules from §0.4 are operative from this moment

### STEP 3 — Create output folder
Create: `new-ideas/{domain_slug}/{project_name_slug}/`

### STEP 4 — Generate all 13 files with frontmatter
Generate the complete set of 13 `.md` files in the order defined in PLAN.md Phase 3.
For every file:
- Add YAML frontmatter (Dynamic File Frontmatter Rule below)
- Apply all Project Rules from PLAN.md relevant to that file type
- Apply all injected skill rules tagged `[SKILL:*]` relevant to that file
- After drafting each file: run the **full Panel of Agents** (4 agents — see below)
  - If any agent raises a BLOCK → GATE 2: regenerate the file
  - If an agent raises a NOTE → log it in the file, continue

### STEP 5 — PLAN.md Phase 3 (Negative Verification)
After all 13 files are drafted:
- Run cross-file attack: goal attack, architecture attack, roadmap attack, assumption attack
- If any attack produces a new BLOCK-level finding → GATE 3: regenerate affected files
- Add verification findings to `11_INTERPOLATION.md` and `03_NEXT_STEPS.md`

### STEP 6 — PLAN.md Phase 4 (Close Gate)
Write `00_INDEX.md` last.
Populate the Verification Status block with actual results.
Only mark the file set `RITROSO-VERIFIED` if all conditions in PLAN.md §Phase 4 are met.

---

## Dynamic File Frontmatter Rule

Every generated file in the final output folder MUST begin with:

```yaml
---
name: "{real_file_name}"
description: "Dynamic agent-facing description built from the file objective, current project context,
inferred limits, declared constraints, anti-patterns, and intended outcome. Must explain how to read
the file, what reasoning approach to prioritize, what limits matter most, and what to do if context
is incomplete. Must reference any active [GOAL-CONFLICT] or [ASSUMED-NO-BASIS] items relevant to
this file. Never generic when context exists."
---
```

### Frontmatter Constraints
- `name` must exactly match the file name
- `description` must never be generic when context exists — it must name the specific project, domain, and key constraints
- `description` must mention the file objective for this specific project
- `description` must reference any active `[GOAL-CONFLICT]` or `[ASSUMED-NO-BASIS]` items relevant to that file
- `description` should guide the consuming agent's reasoning approach explicitly
- If context is weak, description must instruct the agent to rely on linked files and unresolved questions rather than hallucinating

---

## Assumption Tagging Rule

Every assumption made during generation must be tagged:
- `[INFERRED-FROM-TEXT]` — supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support

Every `[ASSUMED-NO-BASIS]` item MUST:
1. Appear in `12_ASKED.md` with tag and justification
2. Have a corresponding risk entry in `10_ERROR.md`
3. List which files would need rewriting if the assumption is wrong

---

## Goal Conflict Rule

If two or more goals in `01_GOAL.md` are found to be in tension:
1. Tag: `[GOAL-CONFLICT: GOAL-A vs GOAL-B]`
2. Document in `11_INTERPOLATION.md`: what conflicts, why, what resolution options exist
3. Do NOT silently resolve by choosing one goal over the other
4. Surface in `12_ASKED.md` as a structural question

---

## Panel of Agents — Full Specification

> Every file is reviewed by all 4 agents before it is closed.
> The agents do not just check — they ATTACK the draft, looking for the most damaging problem they can find.
> Injected skill rules (`[SKILL:*]`) are treated as binding project rules by all agents.
> If any agent raises a BLOCK, the file is regenerated — not annotated.
> Agents can refer findings to each other (cross-referral protocol below).

---

### Agent Interaction Protocol

Agents do not operate in isolation. They collaborate through a structured challenge-and-response:

1. **Sequential pass**: ARCHITECT → DESIGNER → PRAGMATIST → CRITIC run in this order on each file.
2. **Cross-referral**: Any agent can flag a finding as `[REFER:TARGET_AGENT]` when the issue is outside their domain. Example: ARCHITECT finds a budget gap → `[REFER:PRAGMATIST]`. CRITIC finds a UX failure → `[REFER:DESIGNER]`.
3. **Escalation**: A NOTE from one agent that a second agent upgrades to a BLOCK is binding. The file must be regenerated.
4. **Consensus requirement**: If 2+ agents raise BLOCK on the same issue, the entire file set (not just one file) must be reviewed for the same class of problem.
5. **Final arbiter on conflicts between agents**: PRAGMATIST has veto power on scope decisions. ARCHITECT has veto power on technical decisions. Neither can override an explicit user constraint.

---

### 🏛 ARCHITECT

**Core mandate**: Defend the structural integrity of the system across time, scale, and change.

**Attack posture**: Assume the project succeeds and doubles in scope. Assume one developer leaves. Assume the target user changes. Does the architecture survive?

**Interrogation questions per file**:
- `01_GOAL.md` → "If this goal is achieved, what does the system need to handle next? Is the architecture already ready for it, or does it require a full rebuild?"
- `02_PRODUCT.md` → "Is there a single component here whose failure takes down the entire product? Where is the blast radius documented?"
- `03_NEXT_STEPS.md` → "Does P1 assume P2 infrastructure? Can P1 ship without P2 being built first?"
- `04_ELEMENTS.md` → "Are all dependencies declared? Is there a hidden dependency not listed here?"
- `05_COMPONENTS.md` → "Is the stack internally consistent? Can component A be replaced without rewriting component B?"
- `06_PRICE.md` → "Does the pricing model survive a 3× cost increase in the underlying infrastructure?"
- `07_BUDGET.md` → "Is the cost model additive or exponential? What happens when user count ×10?"
- `08_LIMITS.md` → "Are limits enforceable at runtime, or only advisory? Who enforces them?"
- `09_AGENTS.md` → "If the named decision owner is unavailable, who decides? Is there a documented fallback?"
- `10_ERROR.md` → "Is the blast radius of each failure scenario contained? Or does failure in A cascade to B, C, D?"
- `11_INTERPOLATION.md` → "Do the files form a coherent system, or are they independently written and secretly inconsistent?"
- `12_ASKED.md` → "Are the open questions the real blockers, or are they proxies? What is actually unknown?"

**BLOCK conditions (mandatory)**:
- A component in `05_COMPONENTS.md` directly contradicts a limit in `08_LIMITS.md`
- `03_NEXT_STEPS.md` P1 includes infrastructure that belongs in P2 (scope creep into MVP)
- An injected security skill requires a P1 component that is missing from `04_ELEMENTS.md`
- A single point of failure exists in `02_PRODUCT.md` with no fallback in `10_ERROR.md`
- Two components share state in a way that creates an implicit dependency not documented in `11_INTERPOLATION.md`
- `07_BUDGET.md` assumes a scaling model that contradicts the component architecture in `05_COMPONENTS.md`

**NOTE conditions**:
- Stack has no documented upgrade path for P3 features
- `09_AGENTS.md` has no fallback decision owner for any role
- `10_ERROR.md` has failure scenarios but no recovery steps

**Cross-referral targets**:
- Budget issues → `[REFER:PRAGMATIST]`
- Missing user flow → `[REFER:DESIGNER]`
- Security gaps → `[REFER:CRITIC]`

---

### 🎨 DESIGNER

**Core mandate**: Defend the usability and actionability of every file for the humans and agents who will consume it.

**Attack posture**: Imagine someone who did not write this prompt, has never seen this project before, and must act on this file tomorrow. Can they? What is the first thing they get wrong?

**Interrogation questions per file**:
- `01_GOAL.md` → "Can a person who did not write this prompt explain the goal to a non-technical stakeholder in one sentence? If not, the goal is not yet defined."
- `02_PRODUCT.md` → "Is the user flow specific enough that I could wireframe it right now? Name the screens. Name the states. Name the transitions."
- `03_NEXT_STEPS.md` → "Does each step have a concrete, testable output? Not 'implement auth' — what does done look like? Who signs off?"
- `04_ELEMENTS.md` → "Is each element named precisely, or is it a category? 'Database' is a category. 'PostgreSQL 15 with pgvector on Supabase' is an element."
- `05_COMPONENTS.md` → "Can a developer start building from this file without asking a single clarifying question? What question would they ask first?"
- `06_PRICE.md` → "Does the pricing copy communicate value or cost? Is the value proposition visible before the number?"
- `07_BUDGET.md` → "Can a project manager read this and know what to approve? Are line items specific enough to get a real quote?"
- `08_LIMITS.md` → "Are limits expressed as verifiable conditions, not preferences? 'No dark patterns' is a preference. 'No pre-checked consent boxes' is verifiable."
- `09_AGENTS.md` → "Does every role have a named output, not just a title? What does this role produce that the next role consumes?"
- `10_ERROR.md` → "Are failure modes described as concrete events or as vague categories? 'API failure' is a category. 'Stripe webhook arrives after 30s timeout, payment marked pending, user sees error state' is a failure mode."
- `11_INTERPOLATION.md` → "Are the contradictions named, or just implied? Can a reader resolve them without re-reading all 13 files?"
- `12_ASKED.md` → "Does each question state what it blocks? An open question without stakes is noise."

**BLOCK conditions (mandatory)**:
- `03_NEXT_STEPS.md` contains a step with no concrete, testable output
- `02_PRODUCT.md` has no user flow (minimum 3 named steps: entry → action → outcome)
- `09_AGENTS.md` lists a role with no concrete responsibility or output artifact
- An active `[SKILL:ux-ui-pro-max]` or `[SKILL:brand-guidelines]` rule is violated in `05_COMPONENTS.md`
- A limit in `08_LIMITS.md` is not expressed as a verifiable condition (advisory language: "try to", "ideally", "where possible")
- `12_ASKED.md` contains questions with no stated consequence (what does this block if unresolved?)

**NOTE conditions**:
- Jargon is used without definition in any file
- `10_ERROR.md` failure modes use category language instead of specific event descriptions
- `09_AGENTS.md` roles do not specify what they consume (input) and produce (output)

**Cross-referral targets**:
- Architectural inconsistency causing confusion → `[REFER:ARCHITECT]`
- Budget impact of design decision → `[REFER:PRAGMATIST]`
- Security risk from unclear ownership → `[REFER:CRITIC]`

---

### ⚙️ PRAGMATIST

**Core mandate**: Defend the economic and operational feasibility of the entire plan.

**Attack posture**: Assume the budget is real and fixed. Assume the team is exactly as described. Assume external APIs and services cost what they say they cost and fail when they say they won't. Can this actually ship?

**Interrogation questions per file**:
- `01_GOAL.md` → "Is the goal achievable at the declared budget and team size? If not, which goal is the casualty?"
- `02_PRODUCT.md` → "How many engineering hours does the described user flow take to build? Is that consistent with 07_BUDGET?"
- `03_NEXT_STEPS.md` → "Does P1 contain features that require external service agreements, procurement, or legal review not accounted for?"
- `04_ELEMENTS.md` → "Are there elements here that require expertise not present in 09_AGENTS? Who builds them?"
- `05_COMPONENTS.md` → "What is the hourly cost of running this stack at 100 users? At 10,000 users? Is there a break-even point?"
- `06_PRICE.md` → "Does the pricing model generate enough margin to cover 07_BUDGET costs, including infrastructure, support, and maintenance?"
- `07_BUDGET.md` → "Is there a 10% contingency line? Is there a line for unknown unknowns? If not, the budget is optimistic by definition."
- `08_LIMITS.md` → "Do any limits introduce hidden costs? 'No vendor lock-in' is a limit. It may cost 3× to implement."
- `09_AGENTS.md` → "Is the team size consistent with the P1 scope? How many hours per week is each agent expected to contribute?"
- `10_ERROR.md` → "What is the cost of each failure scenario? Recovery time? Lost revenue? Customer support load?"
- `11_INTERPOLATION.md` → "Are there cross-file dependencies that create hidden scope? A dependency between components A and B that is not budgeted?"
- `12_ASKED.md` → "Does resolving any open question change the budget by >20%? Those are not just open questions — they are budget risks."

**BLOCK conditions (mandatory)**:
- P1 scope exceeds the declared budget by >50% (rough estimate required)
- A component in `05_COMPONENTS.md` requires expertise not listed in `09_AGENTS.md`
- `06_PRICE.md` and `07_BUDGET.md` are internally inconsistent (cost > revenue at declared volume)
- Any P1 step in `03_NEXT_STEPS.md` depends on an unresolved OPEN in `12_ASKED.md`
- An injected marketing or analytics skill adds costs not reflected in `07_BUDGET.md`
- `07_BUDGET.md` has no contingency line

**NOTE conditions**:
- Timeline has no buffer for debugging or integration testing
- A service or API has no stated cost estimate in `07_BUDGET.md`
- `09_AGENTS.md` does not specify hours or availability per role

**Cross-referral targets**:
- Over-engineered architecture driving cost → `[REFER:ARCHITECT]`
- Vague requirements making estimation impossible → `[REFER:DESIGNER]`
- Risk-driven costs not captured → `[REFER:CRITIC]`

---

### 🔒 CRITIC

**Core mandate**: Find the most plausible, specific, near-term failure that kills the project before it validates.

**Attack posture**: It is day 31. The project launched 30 days ago. What went wrong? Be specific. Name the failure, the vector, the impact, the person who called it.

**Interrogation questions per file**:
- `01_GOAL.md` → "Is there an assumption in this goal that, if false, makes the entire project irrelevant? Name it."
- `02_PRODUCT.md` → "What is the first thing users will do that the product is not designed for? What happens then?"
- `03_NEXT_STEPS.md` → "Which P1 step has the highest probability of being skipped under deadline pressure? What breaks if it is skipped?"
- `04_ELEMENTS.md` → "Is there an element here that is described as simple but is actually a known hard problem? (e.g., 'real-time sync', 'email deliverability', 'PDF generation')"
- `05_COMPONENTS.md` → "Which component is most likely to be the integration failure point? Where do two components touch without a clear contract?"
- `06_PRICE.md` → "Is there a pricing scenario where the product is used heavily and loses money per user? (high-volume, low-price trap)"
- `07_BUDGET.md` → "What external cost is most likely to be wrong by an order of magnitude? (API costs, cloud egress, support volume)"
- `08_LIMITS.md` → "Which limit is most likely to be violated silently, without anyone noticing until it causes damage?"
- `09_AGENTS.md` → "Who is the single human whose departure or unavailability kills the P1 timeline? Is there a backup?"
- `10_ERROR.md` → "Are the listed failure modes the real risks, or are they the safe, low-embarrassment risks? What is the uncomfortable failure no one wants to write down?"
- `11_INTERPOLATION.md` → "Is there a contradiction between files that has been documented but not resolved? That is not documentation — it is a deferred decision masquerading as documentation."
- `12_ASKED.md` → "Which open question is everyone implicitly assuming will resolve in the optimistic direction? Name the pessimistic scenario."

**BLOCK conditions (mandatory)**:
- `10_ERROR.md` has fewer than 3 concrete, specific failure scenarios (not categories — events)
- A security skill (`[SKILL:*]`) is injected but no security owner is named in `09_AGENTS.md`
- A `[SKILL:*:remote-fetch]` rule covers a hard limit that is NOT present in `08_LIMITS.md`
- An `[ASSUMED-NO-BASIS]` item exists in `12_ASKED.md` with no corresponding risk entry in `10_ERROR.md`
- `10_ERROR.md` contains only "technical" failures — no human, process, or organizational failures documented
- A failure in `10_ERROR.md` has no stated recovery action or mitigation owner

**NOTE conditions**:
- No rollback plan exists for any P1 deployment step
- `12_ASKED.md` has questions that resolve optimistically by default
- `09_AGENTS.md` has a single point of human failure with no documented backup

**Cross-referral targets**:
- Architectural risk enabling attack surface → `[REFER:ARCHITECT]`
- Unclear ownership creating security gap → `[REFER:DESIGNER]`
- Risk with significant budget implication → `[REFER:PRAGMATIST]`

---

## The 13 Files — Templates & Depth Specification

> These templates define the minimum depth required for each file.
> "Minimum depth" means: if a reviewer reads this file and can anticipate a problem
> that the file does not mention, the file is not deep enough.
> Generic sections are forbidden. Every section must be specific to this project.

---

### `00_INDEX.md` — Navigation & Verification Hub

**Purpose**: The entry point for any agent or human reading the file set. It must orient the reader, surface the most critical open issues, and provide the Skill Discovery Log.

**Required sections**:
```
## Project Identity
- Name, domain, generation date, RITROSO version, PLAN.md version
- Domain classification (from §0.2)
- One-line project summary (no jargon)

## Verification Status
- [ ] All 13 files present
- [ ] Panel of Agents: no unresolved BLOCKs
- [ ] Close Gate conditions met (list each with pass/fail)
- Mark: RITROSO-VERIFIED or RITROSO-PENDING with reason

## Critical Open Issues
- List every unresolved BLOCK, GATE, GOAL-CONFLICT, or ASSUMED-NO-BASIS item
- Each item: what it is, which file it lives in, what resolves it
- If none: explicitly state "No unresolved issues"

## File Map
| File | Purpose | Key Constraint | Last Modified |
|------|---------|----------------|---------------|
(one row per file, constraint is the most important limit for that file)

## Skill Discovery Log
(see PLAN.md §0.4.5 template — complete, not abbreviated)

## Skill Stack
(Active / Probable / Recommended tables — see PLAN.md §0.4.5)
```

**Depth requirement**: The Critical Open Issues section must name every unresolved issue by name, not by category. "Several open questions" is not acceptable. "3 ASSUMED-NO-BASIS items in 12_ASKED.md: monetization model, target user age group, and primary distribution channel" is acceptable.

---

### `01_GOAL.md` — Project Goal & Success Criteria

**Purpose**: Define why this project exists, for whom, and how we know if it succeeded.

**Required sections**:
```
## Problem Statement
- The specific problem being solved (not the category of problem)
- Who has this problem (named persona, not demographic category)
- What they currently do instead (workaround, competitor, nothing)
- Why current solutions are insufficient (specific gap, not "they're bad")

## Goal Statement
- Primary goal: one sentence, benefit-first framing
- Secondary goals (max 3): each with explicit relationship to primary
  - Does achieving secondary goal A help or hinder primary goal?
  - If hinder: document as [GOAL-TENSION] not conflict — tension is acceptable; conflict is not

## Success Criteria
- Minimum viable success: what must be true at P1 launch for the project to be worth continuing
- Full success: what must be true at P2 for the project to be considered achieved
- Each criterion must be measurable (number, date, event) — not a quality judgment
- Anti-criteria: what is explicitly NOT success (avoids scope creep)

## Stakeholder Map
- Who benefits directly: named, with their specific benefit
- Who is affected but does not benefit: named, with the impact on them
- Who decides if the project continues after P1: named, with their decision criteria

## Injected rules from active skills
(if copywriting or marketing skill active: value proposition formula applied here)
```

**Depth requirement**: The Problem Statement must name the specific workaround the target user currently uses. "They use existing tools" is not acceptable. "They copy-paste rows between two spreadsheets every Monday morning" is acceptable.

---

### `02_PRODUCT.md` — Product Specification

**Purpose**: Define what the product concretely does, how users interact with it, and what the system must support.

**Required sections**:
```
## Product Summary
- One paragraph: what it is, what it does, who uses it, on what platform
- What it is NOT (explicit boundary — prevents scope creep)

## User Flow — Primary (P1)
Step-by-step from entry point to outcome. Minimum 3 steps, maximum 8 for P1.
For each step:
- User action (what they do)
- System response (what happens)
- Visual/interaction state (loading, error, empty, success)
- Edge cases at this step (what can go wrong for the user here)

## User Flow — Secondary (P2, if known)
Same format. Mark as P2. Do not conflate with P1.

## Feature Scope
P1 features (must ship): each with acceptance criteria
P2 features (post-launch): each with dependency on P1 features
Out of scope: explicitly listed — not just "everything not in P1"

## System Requirements
- Platform: web / mobile / desktop / CLI / API
- Authentication model
- Data persistence requirements
- Real-time requirements (if any): latency budget, consistency model
- Offline requirements (if any)
- Accessibility requirements: minimum standard (WCAG AA / AAA / none — must be explicit)

## Design System Reference
(if ui-ux or website-building skill active: design system named, component library named, anti-patterns listed)

## Injected rules from active skills
(skill-specific product requirements tagged [SKILL:*])
```

**Depth requirement**: Every step in the user flow must name at least one edge case. "User submits form" has the edge case "form has validation error" and "form submits but network drops before server responds." Both must be named.

---

### `03_NEXT_STEPS.md` — Roadmap & Execution Plan

**Purpose**: Define what gets built in what order, who builds it, and what done looks like.

**Required sections**:
```
## Phase 1 — Minimum Viable Product
For each P1 item:
- Task name (verb + noun — not a category)
- Concrete output (what exists when this is done that did not exist before)
- Sign-off condition (how we know it is done — demo, test passing, user reviewed)
- Owner (from 09_AGENTS.md — named role, not "the team")
- Dependency (what must be done before this can start)
- Estimated effort (hours or days — not "medium")
- Blocked by OPEN? (if yes: which question in 12_ASKED.md)

## Phase 2 — Post-Launch
Same format. Each P2 item must have an explicit dependency on a specific P1 item.
P2 items that have no P1 dependency are P1 items that were mislabeled — fix them.

## Phase 3 — Future (optional)
Free-form. No owner, no estimate required. This is the vision, not the plan.

## Critical Path
Which P1 items block all others? Name them. If none, name the item with the most dependents.

## Injected rules from active skills
(e.g., if git-devops skill active: branch strategy and deployment steps are P1 items here)
(if security skill active: security review is P1 — not P2)
(if testing skill active: tests are P1 items, named per component)
```

**Depth requirement**: Every P1 item must have an owner. "Team" is not an owner. If a step has no named owner, it will not be done. The file must name who owns it or flag `[ASSUMED-NO-BASIS: no owner declared]`.

---

### `04_ELEMENTS.md` — Technology & Dependency Stack

**Purpose**: Name every external dependency, service, library, and infrastructure component the system relies on.

**Required sections**:
```
## Dependency Register
For each dependency:
- Name and version (specific — not "latest")
- Role in the system (what it does that cannot be done without it)
- Tier (Critical P1 / Important P1 / Nice-to-have P2 / Future P3)
- Cost (free / paid — if paid: monthly cost at expected volume)
- Failure mode (what happens to the system if this dependency fails or is unavailable)
- Replacement (what replaces it if it must be removed — "no replacement" is valid if documented)
- Owner (who monitors and maintains this dependency — from 09_AGENTS.md)

## Integration Points
- Where does data cross a boundary between two systems?
- At each integration point: data format, latency expectation, error handling strategy
- Which integration point is most likely to be the source of bugs? (must name one)

## Security Surface
- Which dependencies have access to sensitive data?
- Which dependencies require credentials or API keys?
- Where are credentials stored? (must be specific — env var, secrets manager, etc.)

## Injected rules from active skills
(if security skill active: auth, validation, secrets are Critical P1 here)
(if api-backend skill active: API design pattern named, contract format specified)
```

**Depth requirement**: "Database" is not a dependency. "PostgreSQL 15.3 hosted on Supabase (free tier, row limit: 500MB)" is a dependency. Every dependency must be specific enough that a developer could open a browser and start a free trial from this description alone.

---

### `05_COMPONENTS.md` — Architecture & Component Design

**Purpose**: Define the internal structure of the system — how it is built, not what it does.

**Required sections**:
```
## Architecture Overview
- Diagram in text (ASCII or Mermaid — not just a description)
- Data flow: where data enters, how it moves, where it exits
- Separation of concerns: what belongs in frontend / backend / database / external service

## Component Register
For each component:
- Name
- Responsibility (single responsibility — if it does two things, split it)
- Interface (what it receives, what it returns — types if known)
- State it owns (what data it is the source of truth for)
- Tests required (unit / integration / E2E — must be specified, not left to discretion)
- Known complexity (easy / medium / hard — with rationale for hard)
- Injected skill rules relevant to this component

## Coding Standards
- Language and version
- Linter and formatter (named — not "we use a linter")
- Forbidden patterns (specific — e.g., "no console.log in production code", "no any in TypeScript")
- Required patterns (specific — e.g., "all async functions must have explicit error handling")
- Commit message format
- Code review requirement (is it mandatory? who reviews?)

## Injected rules from active skills
(if code-quality skill active: coding standards extracted verbatim here tagged [SKILL:*])
(if frontend-framework skill active: component conventions named here)
(if testing skill active: test coverage requirements per component named here)
```

**Depth requirement**: Every component must specify what tests are required. "Tests TBD" is a BLOCK for CRITIC. If a component has no tests, it must be documented as a conscious decision with a stated risk.

---

### `06_PRICE.md` — Pricing & Revenue Model

**Purpose**: Define what users pay, why they pay it, and whether the model is sustainable.

**Required sections**:
```
## Pricing Model
- Model type: one-time / subscription / usage-based / freemium / marketplace / free
- Tiers (if any): each tier with specific limits and specific value proposition per tier
- What triggers an upgrade from free to paid (the "aha moment" + the limit)

## Value Proposition Per Price Point
- At each price point: what is the user getting that justifies this amount?
- Not feature lists — value delivered (time saved, money made, risk reduced, frustration eliminated)
- Competitor price reference: what do alternatives charge for comparable value?

## Revenue Model
- Unit economics: revenue per user per month at target adoption
- Break-even calculation: (fixed costs) / (margin per user) = users needed to break even
  - If this number is unrealistic, flag as [GOAL-CONFLICT: pricing vs budget]
- Revenue at P1 launch (expected): $X/month (or "no revenue — validation phase")
- Revenue target at P2: $X/month

## Pricing Risks
- High-volume low-margin trap: what happens if 1000 users all use the free tier?
- Price sensitivity: what is the maximum price this market will pay? Source?
- Competitor undercutting scenario: if a competitor halves their price, what is the response?

## Injected rules from active skills
(if copywriting skill active: value-emphasis framing applied, cost-emphasis forbidden [SKILL:*])
(if marketing skill active: positioning framework applied to pricing copy)
```

**Depth requirement**: The break-even calculation must use actual numbers from `07_BUDGET.md`. If the numbers don't exist yet, flag as `[ASSUMED-NO-BASIS]` and estimate with explicit rationale.

---

### `07_BUDGET.md` — Cost & Resource Plan

**Purpose**: Define the full cost of building and running the system.

**Required sections**:
```
## Build Cost (one-time)
Line items:
- Development hours × hourly rate (or opportunity cost if internal)
- Design hours × hourly rate
- Infrastructure setup (one-time: domain, SSL, initial DB setup, CI/CD setup)
- Third-party integrations (one-time setup cost)
- Testing and QA
- Documentation
- 10% contingency (mandatory line item — no exceptions)

## Operating Cost (monthly, at launch)
Line items (each must be specific — no "miscellaneous"):
- Hosting / cloud infrastructure
- Database (storage + compute)
- External APIs (cost per unit × expected volume)
- Support tooling
- Monitoring and alerting
- 10% contingency on monthly costs

## Scaling Cost Model
- Monthly cost at: 10 users / 100 users / 1000 users / 10,000 users
- Identify the step-function costs (where costs jump non-linearly)
- Name the first cost that becomes unsustainable before the business is profitable

## Team Cost
- If paid team: hours × rate × duration per phase
- If solo/founder: opportunity cost acknowledged (even if $0 line item)
- Outsourcing vs. in-house decision documented for each component

## Injected rules from active skills
(if financial-spreadsheet skill active: sensitivity analysis required — what if cost ×3?)
(if marketing skill active: marketing budget as separate line item)
```

**Depth requirement**: The 10% contingency line is non-negotiable. A budget without contingency is a wish list. PRAGMATIST will BLOCK any budget without it.

---

### `08_LIMITS.md` — Hard Limits & Constraints

**Purpose**: Define what cannot be done, what must not be done, and what is structurally impossible given current constraints.

**Required sections**:
```
## Hard Limits (cannot be violated — no exceptions)
For each hard limit:
- The limit stated as a verifiable condition (not a preference)
- Why it exists (technical / legal / business / ethical)
- What triggers a violation (specific — "when X happens, this limit is violated")
- Who enforces it (from 09_AGENTS.md)
- What happens if violated (rollback, incident, escalation)

## Soft Limits (strong preferences — can be overridden with explicit justification)
Same format, plus: "Override requires sign-off from [role]"

## Anti-Features (explicitly out of scope)
Things this system will never do, even if asked:
- Each anti-feature named
- Why: not "out of scope" — specific reason (complexity, ethics, legal, distraction)

## Injected hard limits from active skills
(ALL injected [SKILL:*] limits appear here — tagged, never softened)
(if security skill active: auth, input validation, secrets limits are Hard — no exceptions)
(if code-quality skill active: forbidden patterns are Hard limits)
```

**Depth requirement**: "No dark patterns" is a soft preference. "No pre-checked consent checkboxes, no fake countdown timers, no misleading free trial terms" is a set of hard limits. Every limit must be expressed as a condition that can be checked by a code reviewer or product manager with a yes/no answer.

---

### `09_AGENTS.md` — Team, Roles & Decision Protocol

**Purpose**: Define who makes which decisions, what each role produces, and how conflicts are resolved.

**Required sections**:
```
## Role Register
For each role (human or AI agent):
- Role name
- Mandate (one sentence: what is this role responsible for protecting or producing?)
- Input (what this role consumes to do its work)
- Output (what this role produces that the next role consumes)
- Decision authority (what can this role decide unilaterally?)
- Escalation path (what does this role escalate, and to whom?)
- Availability (full-time / part-time / async — and hours per week if applicable)
- Backup (who covers this role if unavailable?)

## Decision Matrix
For common conflict scenarios, who has the final call:
- Scope vs. timeline conflict → [role]
- Cost vs. quality conflict → [role]
- Security requirement vs. deadline → [role: SECURITY OWNER, always wins]
- User request vs. architectural limit → [role]

## Communication Protocol
- How does the team communicate? (tool: Slack / Linear / GitHub / async docs)
- What decisions require written documentation vs. verbal?
- How are decisions reversed? (who can reverse, under what conditions)

## Panel of Agents — This Project's Instance
(Ritroso Panel of Agents mapped to actual project roles:
 ARCHITECT = [named role or person]
 DESIGNER = [named role or person]
 PRAGMATIST = [named role or person]
 CRITIC = [named role or person])

## Injected rules from active skills
(if security skill active: security owner role is mandatory — no anonymous security)
(if brand skill active: brand guardian role named with explicit approval rights)
(if ai-agents skill active: agent roles, handoff protocols, least-privilege tiers from skill)
```

**Depth requirement**: Every role must specify what it produces. "Product manager" is a title. "Product manager produces: weekly written prioritization decision for P1 backlog, with rationale for any item moved or deprioritized" is a role definition. If the output is not named, the role will not function.

---

### `10_ERROR.md` — Failure Scenarios & Risk Register

**Purpose**: Document every significant failure mode with enough specificity to enable detection and recovery.

**Required sections**:
```
## Failure Scenario Register
For each scenario (minimum 5 — not categories, events):
- Scenario name (noun + event: "Payment webhook timeout")
- Vector: how does this failure happen? (specific mechanism)
- Trigger: what condition causes it? (measurable)
- Impact: what does the user experience? what does the system do?
- Probability: High / Medium / Low — with rationale
- Severity: Critical (data loss, security breach) / High (outage) / Medium (degraded) / Low (cosmetic)
- Detection: how is this failure detected? (monitoring, user report, automated test)
- Recovery: step-by-step — who does what in the first 30 minutes?
- Prevention: what is already in place (or should be) to reduce probability?
- Owner: who is responsible for this scenario (from 09_AGENTS.md)

## Failure Categories Required (minimum one scenario per category)
- Technical failure (infrastructure, API, database)
- Integration failure (two components interacting incorrectly)
- Data failure (corruption, loss, inconsistency)
- Human failure (user error, operator error, team error)
- Process failure (missing step, wrong sequence, ignored checklist)
- Security failure (unauthorized access, data exposure, injection)
- Business failure (pricing model wrong, no adoption, wrong market)

## Injected failure scenarios from active skills
(if security skill active: security scenarios are High probability/High impact by default)
(if security-offensive skill active: attack scenarios in format [ATTACK-N] Vector/Entry/Impact/Mitigation)
(if marketing skill active: no analytics day 1, no acquisition channel, no retention hook)
```

**Depth requirement**: Every failure scenario must have a named owner and a recovery action. "Monitor the situation" is not a recovery action. "On detection: [owner] pages [on-call], rolls back to last known good deployment, posts status update within 15 minutes" is a recovery action.

---

### `11_INTERPOLATION.md` — Cross-File Consistency & Conflict Analysis

**Purpose**: Verify that all 13 files form a coherent system, surface contradictions, and document how they are resolved.

**Required sections**:
```
## Consistency Check
For each pair of files that are semantically coupled:
- File A ↔ File B: what must be consistent between them?
- Current status: consistent / inconsistent / unverified
- If inconsistent: which version is correct, and which file must be updated?

## Conflict Register
For each detected conflict ([GOAL-CONFLICT], [SKILL-CONFLICT], cross-file contradiction):
- Conflict name
- Files involved
- What is in tension (specific)
- Resolution options (enumerate — do not resolve silently)
- Status: open / resolved / deferred (with justification for deferral)

## Dependency Graph
Which files depend on decisions in other files?
- `05_COMPONENTS.md` depends on decisions in: `04_ELEMENTS.md`, `07_BUDGET.md`, `08_LIMITS.md`
- (map the actual dependencies for this project — not the template)

## Assumption Chain Analysis
- Which assumptions, if wrong, invalidate the most other files?
- Rank top 3 most-consequential assumptions
- For each: which files would need to be rewritten?

## Negative Verification Results
(from PLAN.md Phase 3 attacks — results must appear here)
- Goal attack result
- Architecture attack result
- Roadmap attack result
- Assumption attack result
```

**Depth requirement**: The Consistency Check must cover every pair of files that share a decision. The template pairs above are the minimum. If a project has a specific coupling not listed (e.g., a pricing limit that constrains the architecture), it must be added.

---

### `12_ASKED.md` — Open Questions & Unresolved Issues

**Purpose**: Document everything that is not yet known, with explicit stakes for each unknown.

**Required sections**:
```
## Open Questions Register
For each open question:
- Question (specific — not a category)
- Tag: [INFERRED-FROM-TEXT] or [ASSUMED-NO-BASIS]
- What we assumed (the default assumption in the generated files)
- Why this matters: what changes if the assumption is wrong?
  - Which files need rewriting?
  - By how much does the budget change?
  - Does the architecture change?
- Who can answer this question? (named person or role)
- Deadline for answer: at what point does this become a blocker?
- Current status: open / answered / deferred

## Blocking Questions (answer before P1 launch)
Subset of questions that, if unresolved, make P1 launch unsafe or pointless.
Each must be flagged as BLOCKING with explicit rationale.

## Non-Blocking Questions (can be answered post-launch)
Questions that represent P2 design decisions with no P1 impact.

## Skill-Specific Open Questions
(if marketing skill active: ICP defined? Acquisition channel? Retention hook?)
(if security skill active: threat model complete? Auth design reviewed? Secrets rotation planned?)
(if any skill active with domain-specific open questions: they appear here)
```

**Depth requirement**: Every question must state what it blocks. An open question with no stated consequence is noise and will be ignored. The file must be pruned of noise before the Close Gate.

---

## File Interdependency Map

The following pairs have mandatory consistency requirements. ARCHITECT checks these at Phase 3:

| Source file | Target file | What must be consistent |
|---|---|---|
| `01_GOAL.md` | `06_PRICE.md` | Goal audience = pricing target audience |
| `02_PRODUCT.md` | `04_ELEMENTS.md` | Every P1 feature has a supporting element |
| `02_PRODUCT.md` | `03_NEXT_STEPS.md` | Every P1 feature has a delivery step |
| `05_COMPONENTS.md` | `07_BUDGET.md` | Component complexity = budget estimate |
| `05_COMPONENTS.md` | `08_LIMITS.md` | No component violates a hard limit |
| `07_BUDGET.md` | `06_PRICE.md` | Revenue model covers operating costs |
| `09_AGENTS.md` | `03_NEXT_STEPS.md` | Every P1 step has an owner in 09_AGENTS |
| `10_ERROR.md` | `09_AGENTS.md` | Every failure scenario has an owner in 09_AGENTS |
| `12_ASKED.md` | `10_ERROR.md` | Every ASSUMED-NO-BASIS has a risk entry |
| `08_LIMITS.md` | `05_COMPONENTS.md` | No component references a forbidden pattern |

---

## Multimodal Input

The system is multimodal: reads text, code, images, PDFs, video, GitHub repos,
and any resource available in context. All inputs feed the inference loop and
the context-gap protocol before generation begins.

---

## Version History
| Version | Change |
|---------|--------|
| v1 | Initial skill — 13 files, inference loop, frontmatter |
| v2 | Added context-gap protocol |
| v3 | Added negative verification, dynamic frontmatter constraints |
| v4 | Bound PLAN.md as mandatory protocol; added Panel of Agents, assumption tagging, goal conflict rule, gate system |
| v5 | Full Panel of Agents spec with attack posture and cross-referral protocol; full 13-file templates with depth requirements; File Interdependency Map; agent collaboration and escalation rules |
