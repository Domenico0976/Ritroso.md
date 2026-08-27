---
name: "Ritroso"
version: "6.1"
description: "Ritroso v6.1: generates a complete 13-file .md set for any project (software, creative, content, open-source, service). Includes MODEL REASONING, SUB-AGENT QUERIES, SELF-INTERROGATION per file, inference loop, Panel of 4 Agents (ARCHITECT / DESIGNER / PRAGMATIST / CRITIC), Skill Discovery & Injection Engine (5 methods including remote-fetch), blocking-question protocol, post-generation quality check (encoding, goal-pricing consistency, budget alignment, role deduplication), and retroactive self-verification. This file is self-contained — no companion files are required."
applies_to: "All Ritroso file-set generations"
---

# RITROSO — Contextual Reasoning Skill v6.0

> Contextual reasoning skill for LLMs with multimodal generation of complete `.md` file sets,
> inference loop with per-file MODEL REASONING / SUB-AGENT QUERIES / SELF-INTERROGATION,
> Panel-of-Agents validation, cross-agent attack protocols, Skill Discovery Engine,
> and retroactive self-verification.
>
> **Mantra**: create the most reliable instructions possible.

---

## What this file is

This file is **fully self-contained**. It defines:
- **What** Ritroso does (the 13-file structure, each file's content contract)
- **How** to execute (phases, gates, inference loop, skill discovery, panel review)
- **The complete template** for every generated file

No companion files are required. The protocol below IS the skill.

---

## What Ritroso Generates

For every input prompt, Ritroso produces a folder with **13 structured `.md` files**:

| # | File | Contains |
|---|------|----------|
| 00 | `00_INDEX.md` | Navigation hub, verification status, Skill Discovery Log, Skill Stack, **Panel table (written LAST)** |
| 01 | `01_GOAL.md` | Problem statement, success criteria, stakeholder map |
| 02 | `02_PRODUCT.md` | Product spec, user flows (P1/P2), feature scope, system requirements |
| 03 | `03_NEXT_STEPS.md` | Phase-by-phase roadmap with owners, outputs, sign-off conditions (**mandatory — absence = hard failure**) |
| 04 | `04_ELEMENTS.md` | All required elements: Critical / Important / Optional |
| 05 | `05_COMPONENTS.md` | Full tech stack, component contracts, injected skill rules |
| 06 | `06_PRICE.md` | Pricing strategy, tiers, value framing |
| 07 | `07_BUDGET.md` | Build cost, infrastructure cost, contingency (10% mandatory) |
| 08 | `08_LIMITS.md` | Hard limits tagged `[SKILL:name]` on every line, injected skill hard limits, forbidden patterns |
| 09 | `09_AGENTS.md` | Decision owners, roles, responsibilities, fallback owners, handoff protocol, escalation paths |
| 10 | `10_ERROR.md` | ≥3 concrete failure scenarios with Probability/Impact/Detection/Prevention per scenario |
| 11 | `11_INTERPOLATION.md` | Cross-file contradictions, `[GOAL-CONFLICT: ...]`, `[SKILL-CONFLICT: ...]` — explicit none-verification mandatory |
| 12 | `12_ASKED.md` | All open questions tagged `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]` with structured 4-field format |

**Output folder**: `new-ideas/{domain_slug}/{project_name_slug}/`

---

## Execution Order

Execute these steps in order. Do not skip or reorder.

### STEP 1 — Phase 0: Prompt Intake, Classification & Blocking Questions

Read the prompt exactly as written. Do not upgrade vocabulary, do not infer intent.

**Classify domain** (pick one):
- `software-product` — app, SaaS, tool, API
- `creative-technical` — audiovisual, generative art, interactive installation
- `content-operations` — editorial, CMS, workflow, publishing
- `open-source-framework` — library, skill, prompting framework, dev tool
- `service-or-agency` — consulting deliverable, client project
- `other` — document explicitly

**Ask one blocking question at a time** using the priority order below.
Do NOT count ambiguities and apply a numeric gate. Instead, identify which context gap is most blocking and ask about it. Stop and wait after each question.

**Gap priority order**:
1. `goal_gap` — what problem does this solve, for whom?
2. `constraint_gap` — budget, time, team size, platform lock-in?
3. `antipattern_gap` — what must NOT be done?
4. `user_gap` — who are the users? specific roles?
5. `stack_gap` — what technology / architecture?

**Minimum viable context** before proceeding:
- `goal_gap`: RESOLVED (confidence HIGH or MEDIUM)
- `constraint_gap`: RESOLVED or PARTIAL with explicit `[OPEN]` flag

If the user's prompt already provides sufficient answers to all 5 gap types → proceed directly to Step 2.

### STEP 2 — Phase 0.4: Skill Discovery & Injection Engine

Discovery is **never blocking**. It enriches generation. Always attempt all methods.

| Method | Trigger | Action |
|--------|---------|--------|
| 1 — Context scan | Always first | Check session for SKILL.md files, mentions, `load_skill()` calls, YAML frontmatter |
| 2 — Direct path scan | After Method 1 | Scan all known skill paths (see table below) |
| 3 — Grep fallback | If Method 2 returns 0 | Run recursive search from project root and home |
| C — Remote Fetch | If Methods 1-3 return 0 AND agent has HTTP | Auto-fetch raw URLs from catalog (max 5, project-relevant only) |
| 4 — Inference | If all above return 0 | Check config files, package.json, import statements for skill names |

**Known skill paths (scan all):**

```
# WINDOWS
%USERPROFILE%\.claude\skills\
%APPDATA%\Claude\skills\
%LOCALAPPDATA%\Claude\skills\
<PROJECT_ROOT>\.claude\skills\
<PROJECT_ROOT>\skills\
<PROJECT_ROOT>\claude\skills\

# macOS
~/.claude/skills/
~/Library/Application Support/Claude/skills/
<PROJECT_ROOT>/.claude/skills/
<PROJECT_ROOT>/skills/
<PROJECT_ROOT>/claude/skills/

# Linux
~/.claude/skills/
~/.config/claude/skills/
~/.local/share/claude/skills/
<PROJECT_ROOT>/.claude/skills/
<PROJECT_ROOT>/skills/
<PROJECT_ROOT>/claude/skills/

# Cross-platform agents
<PROJECT_ROOT>/.codex/skills/
<PROJECT_ROOT>/.gemini/skills/
<PROJECT_ROOT>/.opencode/skills/
<PROJECT_ROOT>/agent-skills/
<PROJECT_ROOT>/llm-skills/
```

For each path: list subdirectories → check for `SKILL.md` (case-insensitive) → read file → extract `name`, `description`, `version` from YAML frontmatter → mark ACTIVE.

**Result classification:**

| Status | Meaning | Action |
|--------|---------|--------|
| ACTIVE | SKILL.md content in context or read from disk | Inject rules into target files |
| ACTIVE (remote-fetch) | SKILL.md fetched from raw URL | Inject with `[SKILL:name:remote-fetch]` tag; suggest install in INDEX |
| PROBABLE | Name inferred but content not read | List in INDEX, note unverified |
| ABSENT | Not found by any method | Recommend in INDEX with install instructions |

**User-defined skills always take priority.** Never override with a catalog skill covering the same category.

**Injection rules (mandatory):**
1. Inject rules, not pointers. Write the actual operative rules inline in target files.
2. Tag every injected rule with `[SKILL:skill-name]` or `[SKILL:skill-name:remote-fetch]`.
3. Injected rules in `08_LIMITS.md` are always Hard Limits regardless of source classification.
4. Conflict between two injected skills → flag as `[SKILL-CONFLICT: skill-a vs skill-b]` in `11_INTERPOLATION.md`.
5. Document all active skills in `00_INDEX.md` under `## Skill Stack — Active`.

**Skill categories covered (13):**

| Category | Key Skills | Injects Into |
|----------|-----------|-------------|
| `code-quality` | impeccable, simplify, code-reviewer | 05, 08, 03, 10 |
| `ui-ux-design` | ux-ui-pro-max, frontend-design | 02, 04, 05, 08, 09, 10 |
| `frontend-framework` | react-component-generator, nextjs-patterns | 05, 08, 03 |
| `api-backend` | api-design, auth-flow, crud-generator | 04, 05, 08, 10 |
| `brand-styleguide` | brand-guidelines, ponytail | 02, 05, 08, 09 |
| `copywriting-content` | content-research-writer, brainstorming | 01, 02, 06, 08, 10 |
| `marketing-growth` | lead-research-assistant, competitive-ads-extractor | 01, 02, 03, 06, 07, 10, 12 |
| `security-defensive` | webapp-testing, llm-sast-scanner, auth-flow | 03, 04, 05, 08, 09, 10 |
| `security-offensive` | resemble-detect, computer-forensics, ffuf-web-fuzzing | 08, 10, 11, 03 |
| `testing-qa` | test-driven-development, tdd-guide, webapp-testing | 03, 05, 10 |
| `data-analytics` | csv-data-summarizer, d3js-visualization, postgres | 03, 04, 05, 08, 10 |
| `document-writing` | doc, pdf, markdown-to-epub | 01, 03, 09 |
| `financial-spreadsheet` | xlsx, invoice-organizer | 06, 07 |
| `ai-agents-mcp` | mcp-builder, skill-creator, maestro-orchestrate | 05, 08, 09 |
| `git-devops` | git-pushing, using-git-worktrees, deploy-checklist | 03, 08, 09 |

### STEP 3 — Phase 1: Inference Loop (internal, before writing any file)

For each file slot, internally ask:

| File | Question |
|------|----------|
| 01_GOAL | Why does this exist? For whom? How is success measured? |
| 02_PRODUCT | What does it concretely do? User flow in 3 steps? |
| 03_NEXT_STEPS | Smallest thing that proves concept works? |
| 04_ELEMENTS | What must exist for any of this to function? |
| 05_COMPONENTS | Is the stack consistent with 07_BUDGET and 08_LIMITS? |
| 06_PRICE | What does the user pay? Consistent with 01_GOAL? |
| 07_BUDGET | What does it cost? Consistent with 05_COMPONENTS? |
| 08_LIMITS | What cannot be done? Any limits conflicting with 01_GOAL? |
| 09_AGENTS | Who decides when there's a trade-off? Name the owner. |
| 10_ERROR | Most likely failure? Worst-case failure? |
| 11_INTERPOLATION | Do all files point the same direction? Any conflicts? |
| 12_ASKED | What do we not know? Tag each INFERRED or ASSUMED. |

Tag every assumption:
- `[INFERRED-FROM-TEXT]` — supported by the prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support → must also appear in `10_ERROR`

If two goals conflict → flag `[GOAL-CONFLICT]` in `11_INTERPOLATION.md`. Do not resolve silently.

**Cross-domain consistency check (mandatory, runs alongside the inference loop):**
Before writing any file, verify these cross-file relationships are consistent:
1. **Goal ↔ Pricing**: Success metrics in `01_GOAL` must be compatible with the pricing model in `06_PRICE`. If pricing says "no subscription", metrics cannot reference "recurring purchasers" or "MRR" without explicit qualification (e.g. "repeat one-time purchasers"). If pricing is one-time-only, revenue projections must not assume recurring revenue.
2. **Budget alignment**: Phase budgets in `03_NEXT_STEPS` must relate clearly to the total in `07_BUDGET`. If phase budgets include founder time valuation, state the hourly rate and total hours. If they don't, state "founder time unpaid — not reflected in infrastructure budget of €X". Never leave the relationship unexplained.
3. **Role deduplication**: In `09_AGENTS`, no two distinct role entries may refer to the same person. If the same individual holds multiple functions, merge them into a single role with combined responsibilities. "Founder / Product owner" and "Founder / Builder" are the same person — write one role "Founder" with both responsibility sets.
4. **Encoding sanity**: After generating all files, scan every file for replacement characters (U+FFFD, displayed as `?` or mojibake patterns like `Ã©` where `é` is expected), and non-ASCII control characters. If any are found → flag as encoding error and regenerate the affected file with explicit UTF-8 encoding.

**Blocking questions during inference**: if any inference step reveals a critical unknown that blocks coherent generation, STOP and ask the user ONE targeted question. Wait for the answer, then continue. Never proceed with a known critical gap.

### STEP 4 — Create output folder

```
new-ideas/{domain_slug}/{project_name_slug}/
```

### STEP 5 — Generate files (in order, 01 through 12)

Generate in this exact order. Do not merge files. Each file follows the DEEP TEMPLATE specified below.

Every generated file MUST begin with the **Project Header**:

```yaml
---
name: "{actual_filename}"
description: "Project-specific. Names the actual project, domain, key constraints, active [GOAL-CONFLICT] or [ASSUMED-NO-BASIS] items relevant to this file. Never generic."
---
```

Then the rendered project header:

```markdown
# {filename} — {project_name}
Domain: {domain_slug} · Generated: {date} · Ritroso v6.1
```

**File generation order (01→12, INDEX last):**
```
01_GOAL.md → 02_PRODUCT.md → 03_NEXT_STEPS.md → 04_ELEMENTS.md
→ 05_COMPONENTS.md → 06_PRICE.md → 07_BUDGET.md → 08_LIMITS.md
→ 09_AGENTS.md → 10_ERROR.md → 11_INTERPOLATION.md → 12_ASKED.md
```

**⚠ 03_NEXT_STEPS is MANDATORY — do not skip.** Generation cannot proceed past file 03 without completing it.

After each file, run a self-check: "Does this file contradict any other file already written?" If yes → resolve before moving to the next file.

### STEP 6 — Panel of Agents (every file, before closing it)

Every file passes through all 4 agents. Agents ATTACK the draft — they look for the most damaging problem they can find.

| Agent | Mandate | BLOCK condition examples |
|-------|---------|-------------------------|
| 🏛 ARCHITECT | Structural integrity across time, scale, change | Component contradicts a limit in 08_LIMITS; P1 includes P2 infrastructure; single point of failure with no fallback in 10_ERROR; **success metrics in 01_GOAL reference revenue type incompatible with pricing model in 06_PRICE** |
| 🎨 DESIGNER | Usability and actionability | Step in 03_NEXT_STEPS has no concrete output; 02_PRODUCT has no user flow (min 3 steps); role in 09_AGENTS has no concrete responsibility; **encoding errors or replacement characters (U+FFFD) in any generated file** |
| ⚙️ PRAGMATIST | Economic and operational feasibility | P1 scope exceeds budget by >50%; 06_PRICE and 07_BUDGET inconsistent; **phase budgets in 03_NEXT_STEPS not explained relative to total budget in 07_BUDGET**; 07_BUDGET has no contingency line; P1 step depends on unresolved OPEN in 12_ASKED |
| 🔒 CRITIC | Find the most plausible near-term failure | 10_ERROR has <3 concrete failure scenarios; [ASSUMED-NO-BASIS] in 12_ASKED with no risk entry in 10_ERROR; 10_ERROR has only technical failures (no human/organisational); **same person appears as multiple distinct roles in 09_AGENTS instead of being merged into one role** |

Injected skill rules (`[SKILL:*]`) are binding project rules for all agents.

**BLOCK → regenerate the file. NOTE → log in file, continue.**

### STEP 7 — Negative Verification (cross-file attack)

After all 13 files are generated:
- Cross-file attack: goal / architecture / roadmap / assumption
- New BLOCK-level finding → regenerate affected files
- Add verification findings to `11_INTERPOLATION.md` and `03_NEXT_STEPS.md`

### STEP 8 — Write 00_INDEX.md LAST

`00_INDEX.md` is generated after ALL other files are complete. It serves as the master navigation and verification surface.

Structure:
- Project overview (one paragraph)
- File map table with per-file status
- Skill Discovery Log (method used, OS detected, all paths scanned, remote-fetch attempts, install attempts)
- Skill Stack — Active (table: skill, source, category, files injected, rules count)
- Skill Stack — Recommended (ABSENT skills with install instructions)
- Panel of Agents — Validation Log (per-file table: FILE | ARCHITECT | DESIGNER | PRAGMATIST | CRITIC | STATUS)
- Verification Status block

### STEP 8.5 — Post-generation Quality Check (before Close Gate)

After all 13 files are written and 00_INDEX is complete, run this check on every generated file:

1. **Encoding scan**: search every file for replacement characters (U+FFFD), mojibake patterns (`Ã©`, `Ã¨`, `Ã²`, `Ã«`, `Ã¹` where `é`, `è`, `ò`, `ë`, `ù` are expected), and non-printable control characters (except `\n`, `\r`, `\t`). If found → flag the affected files, determine the corruption source, and regenerate those files with explicit UTF-8 encoding.
2. **Goal-Pricing consistency**: read `01_GOAL.md` success metrics and `06_PRICE.md` pricing model. Check for contradictions:
   - "recurring purchaser" / "subscription" / "MRR" in goals when pricing is one-time-only
   - "monthly revenue" projections when pricing has no recurring component
   - Any metric that assumes a billing frequency not offered by the pricing model
   If contradiction found → flag as `[GOAL-CONFLICT: 01_GOAL vs 06_PRICE]`, revise the incompatible metric, and regenerate `01_GOAL.md`.
3. **Budget alignment**: read `03_NEXT_STEPS` phase budgets and `07_BUDGET` total. Check that each phase budget's relationship to the total is stated (e.g. "€X founder time at €25/h = Y hours, unpaid" or "€X infrastructure cost only"). If unexplained → add the explanation and note it in `07_BUDGET.md` budget assumptions.
4. **Role deduplication**: scan `09_AGENTS.md` role names and owners. If the same person appears under multiple role names (e.g. "Founder / Product owner" AND "Founder / Builder") → merge into a single role entry with combined responsibilities. Regenerate `09_AGENTS.md` and update any references in other files.

If any check fails → fix the issue, then re-run the check. Do not proceed to Close Gate until all checks pass.

### STEP 9 — Close Gate

All conditions MUST pass:

- [ ] All 13 files generated (including `03_NEXT_STEPS` — absence = hard failure)
- [ ] `00_INDEX.md` written LAST with full Skill Discovery Log and per-file Panel table
- [ ] `08_LIMITS.md` has at least one hard limit per injected skill, every limit tagged `[SKILL:name]`
- [ ] `11_INTERPOLATION.md` contains explicit `[SKILL-CONFLICT: ...]` and `[GOAL-CONFLICT: ...]` lines
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without the structured 4-field format
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without a matching entry in `10_ERROR`
- [ ] Panel of Agents: no open BLOCKs (verified per-file in Index table)
- [ ] **Goal-Pricing consistency**: success metrics in `01_GOAL` are compatible with the pricing model in `06_PRICE` (no "recurring purchasers" if pricing is one-time-only, no MRR if no subscription, etc.)
- [ ] **Budget alignment**: phase budgets in `03_NEXT_STEPS` are explained relative to total budget in `07_BUDGET` (rate × hours, or explicit "unpaid" notation)
- [ ] **Role deduplication**: `09_AGENTS` has no duplicate person-role pairs (same individual listed under different role names — merge into one role)
- [ ] **Encoding validation**: no replacement characters (U+FFFD), mojibake, or non-UTF-8 artifacts in any generated file

If any condition fails → return to the relevant phase and resolve before declaring done.

---

## Project Header Rule (applies to all 13 files)

Every generated file MUST begin with:

```yaml
---
name: "{actual_filename}"
description: "Project-specific. Names the actual project, domain, key constraints, active [GOAL-CONFLICT] or [ASSUMED-NO-BASIS] items relevant to this file. Never generic."
---
```

Then the rendered project header:

```markdown
# {filename} — {project_name}
Domain: {domain_slug} · Generated: {date} · Ritroso v6.1
```

---

## Assumption Tagging Rule

Every assumption must be tagged:
- `[INFERRED-FROM-TEXT]` — supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support

Every `[ASSUMED-NO-BASIS]` item MUST:
1. Appear in `12_ASKED.md` with the structured 4-field format below
2. Have a corresponding risk entry in `10_ERROR.md`
3. List which files would need rewriting if the assumption is wrong

### Structured format for [ASSUMED-NO-BASIS] in 12_ASKED.md

```
- [ASSUMED-NO-BASIS] {claim}
  → Files affected: {comma-separated list of file names}
  → Scope impact: {low / medium / high}
  → Entry in 10_ERROR: {exact scenario name as written in 10_ERROR}
```

---

## Goal Conflict Rule

If two or more goals in `01_GOAL.md` are in tension:
1. Tag: `[GOAL-CONFLICT: GOAL-A vs GOAL-B]`
2. Document in `11_INTERPOLATION.md`: what conflicts, why, resolution options
3. Do NOT silently resolve by choosing one goal
4. Surface in `12_ASKED.md` as a structural question

---

## DEEP TEMPLATES — Required structure per file

> These are the minimum required sections for each file.
> Each section must be substantive — bullet-point summaries are not sufficient.
> Write as if the reader has never seen the prompt.

---

### 01_GOAL.md

```markdown
# 01 — Goal

## Purpose
Narrative paragraph (3–5 sentences): the problem, who has it, why existing solutions fail,
and why this project addresses it differently. No feature list here — only problem framing.

## Target user
- Primary: [specific person, not a demographic] — what they do, what they need, what they hate
- Secondary (if any): [different use case, distinct from primary]

## Core promise
One sentence: what the user gets and how fast.

## Success metrics
Define success concretely. At least 3 measurable metrics:
- P1 success: [specific measurable outcome at launch]
- P2 success: [outcome at 3 months]
- Failure signal: [what would indicate the project should be abandoned]

## Positioning
One sentence: "For [target user] who [problem], [project name] is the [category]
that [key differentiator]. Unlike [alternative], we [unique approach]."

## Out of scope (v1)
Explicit list of what this project does NOT do in the first version.
This prevents scope creep and anchors PRAGMATIST checks.
```

---

### 02_PRODUCT.md

```markdown
# 02 — Product

## What it does — one paragraph
Plain language description of the product. A non-technical stakeholder must understand
this after one reading. No jargon. No buzzwords.

## User flows — P1 (mandatory, min 3 flows)

### Flow 1: [name]
- Entry point: [how the user arrives at this flow]
- Step 1: [action] → [system response]
- Step 2: [action] → [system response]
- Step 3: [action] → [system response]
- Outcome: [what the user has accomplished]
- Error state: [what happens if step 2 fails]

### Flow 2: [name]
[same structure]

### Flow 3: [name]
[same structure]

## User flows — P2 (planned, not built in P1)
List with one-line description each. No full flow needed — just names and outcomes.

## Feature scope

### P1 — Must ship
| Feature | Description | Acceptance criteria |
|---------|-------------|---------------------|
| [name] | [what it does] | [how we know it works] |

### P2 — Planned next
| Feature | Description | Depends on |
|---------|-------------|------------|
| [name] | [what it does] | [P1 feature or external dependency] |

### Out of scope
Explicit list. If it's not here or in P1/P2, it does not exist.

## System requirements
- Platform: [web / mobile / desktop / API / CLI / other]
- Minimum viable device: [browser version / OS / hardware]
- Offline support: [yes / no / partial — specify]
- Accessibility: [WCAG level targeted]
- Performance target: [e.g. "page load < 2s on 4G"]
```

---

### 03_NEXT_STEPS.md

```markdown
# 03 — Next Steps

> This file is the prerequisite for all PRAGMATIST and DESIGNER checks.
> Every step must have a concrete, testable output and a named owner.
> **MANDATORY — do not skip. Generation cannot proceed without this file.**

## Phase 1 — [name, e.g. "MVP / Proof of Concept"]
**Goal**: [one sentence — what P1 proves or delivers]
**Duration**: [estimated weeks]
**Budget**: [allocated from 07_BUDGET]
**Sign-off condition**: [the single thing that must be true to call P1 done]

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 1.1 | [concrete action] | [role from 09_AGENTS] | [file / demo / endpoint / test] | [verifiable condition] |
| 1.2 | [concrete action] | [role] | [file / demo / endpoint / test] | [verifiable condition] |
| 1.3 | [concrete action] | [role] | [file / demo / endpoint / test] | [verifiable condition] |

## Phase 2 — [name]
**Goal**: [one sentence]
**Duration**: [estimated weeks]
**Prerequisite**: P1 sign-off complete
**Budget**: [allocated]

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 2.1 | [concrete action] | [role] | [file / demo / endpoint / test] | [verifiable condition] |

## Phase 3 — [name or "Future / Backlog"]
High-level only. No detailed steps until P2 is done.
- [item 1]
- [item 2]

## Dependencies and blockers
List anything external that could delay a step:
- [dependency] → blocks [step #] → mitigation: [action]

## Decisions deferred
List decisions that were NOT made here and where they will be made:
- [decision] → deferred to [phase / file / owner] because [reason]
```

---

### 04_ELEMENTS.md

```markdown
# 04 — Elements

> Everything that must exist for the product to function.
> "Critical" = P1 cannot ship without it. "Important" = P2 requires it. "Optional" = nice to have.

## Critical (P1 — must exist at launch)
| Element | Why critical | Owner | Dependency |
|---------|-------------|-------|------------|
| [name] | [specific reason — not "it's important"] | [role] | [what it depends on] |

## Important (P2 — required for full product)
| Element | Why important | Owner | Blocked by |
|---------|--------------|-------|------------|
| [name] | [reason] | [role] | [P1 element or decision] |

## Optional (P3 or never)
| Element | Rationale for deferral |
|---------|----------------------|
| [name] | [why not P1 or P2] |

## Element interaction map
Describe how Critical elements depend on each other.
Format: "[Element A] requires [Element B] to be [state] before [Element A] can [action]."
List at minimum 3 interactions for non-trivial products.

## Injected elements (from active skills)
For each ACTIVE skill that adds elements:
- [element name] [SKILL:skill-name] → Priority: [Critical/Important] → Reason: [from skill rule]
```

---

### 05_COMPONENTS.md

```markdown
# 05 — Components

## Architecture pattern
Name: [monolith / layered / event-driven / microservice / serverless / hybrid]
Justification: [one sentence tied to a specific constraint — budget, team size, latency, scale]
Anti-pattern rejected: [name the rejected architecture and the constraint that ruled it out]

## Tech stack
| Layer | Technology (real name) | Version/Tier | Justification (constraint or element it serves) |
|-------|----------------------|--------------|------------------------------------------------|
| Frontend | [name] | [version] | [why this, not alternatives] |
| Backend | [name] | [version] | [why] |
| Database | [name] | [version] | [why] |
| Auth | [name] | — | [why] |
| Hosting | [name] | — | [cost ref: 07_BUDGET] |
| CI/CD | [name] | — | [pipeline description] |

## Component contracts
For each major component, define its interface contract:

### [Component name]
- **Responsibility**: [one sentence — what this component owns]
- **Inputs**: [what it receives]
- **Outputs**: [what it produces]
- **Failure mode**: [what happens when it fails, reference 10_ERROR]
- **Dependencies**: [other components it calls]

## Injected rules — [skill-name] [SKILL:skill-name]
Extract and write inline the operative rules from each ACTIVE skill relevant to this file.
Do NOT write "see skill X" — write the actual rules here.

Example format:
- No magic numbers — all constants must be named and typed. [SKILL:impeccable]
- Every async function must have explicit error handling. [SKILL:impeccable]
- Components must be ≤200 lines — split beyond that. [SKILL:impeccable]

## Single point of failure
- Component: [name]
- Failure mode: [what breaks and why — specific to this stack]
- Cascade: [A fails → B blocked → C cannot complete — at least 2 levels]
- Detection: [how observable before users are affected]
- Mitigation status: NONE | PARTIAL ({what exists}) | COVERED ({mechanism})

## Forbidden patterns
List explicitly what must NOT be used in this stack:
- [pattern] — reason: [why it's forbidden, reference 08_LIMITS if applicable]
```

---

### 06_PRICE.md

```markdown
# 06 — Price

## Pricing model
Describe the monetization approach in one paragraph: what is sold, to whom,
at what frequency, and what the value proposition is at each price point.

## Tiers
| Tier | Price | What's included | Target user | Revenue assumption |
|------|-------|-----------------|-------------|-------------------|
| Free / Trial | €0 | [limits] | [who] | acquisition |
| [Tier 1] | €X/mo | [features] | [who] | [% of users] |
| [Tier 2] | €X/mo | [features] | [who] | [% of users] |

## Value framing
For each paid tier, write the value statement:
"[User type] pays €X/mo because it saves them [Y hours / €Z / N steps] per [period]."

## Pricing assumptions
List the assumptions this pricing model depends on:
- [assumption] — risk level: [low/medium/high] — reference in 12_ASKED: [item name]

## Revenue projection (P1)
Conservative estimate at end of P1:
- Target paying users: [N]
- ARPU: €[X]
- MRR at P1 close: €[Y]
- Break-even at: [N users / month M]
```

---

### 07_BUDGET.md

```markdown
# 07 — Budget

## Build cost — P1
| Item | Type | Cost | Frequency | Notes |
|------|------|------|-----------|-------|
| [role/contractor] | Labour | €X | one-time / monthly | [scope] |
| [infrastructure] | Fixed | €X | monthly | [provider, spec] |
| [tool/service] | Variable | €X | monthly | [usage estimate] |
| **Subtotal P1** | | **€X** | | |
| **Contingency (10%)** | | **€X** | | mandatory |
| **TOTAL P1** | | **€X** | | |

## Build cost — P2 (estimate)
| Item | Cost estimate | Confidence |
|------|--------------|------------|
| [item] | €X | [high/medium/low] |
| **TOTAL P2 estimate** | **€X** | |

## Infrastructure (recurring post-launch)
| Service | Cost/mo | Scales with | Limit before upgrade |
|---------|---------|-------------|----------------------|
| [service] | €X | [users/requests] | [threshold] |

## Budget assumptions
- [assumption] → if wrong, impact: €[delta] on [P1/P2]
- Contingency covers: [list of known unknowns it absorbs]

## Budget vs Price consistency check
- Total build cost: €X
- Break-even at MRR: €Y (ref: 06_PRICE)
- Months to break-even at projected growth: [N]
- Flag if inconsistent with 06_PRICE: [YES: explain / NO: consistent]
```

---

### 08_LIMITS.md

```markdown
# 08 — Limits

> Every line in this file is a hard limit.
> Hard limit = if violated, the generation or the product is invalid.
> **Every limit MUST carry a [SKILL:name] tag on the same line.**
> A limit without a tag is treated as unverified by CRITIC and will BLOCK regeneration.

## Hard limits — scope
- [limit statement] [SKILL:ritroso]
- [limit statement] [SKILL:ritroso]

## Hard limits — technical
- [limit statement] [SKILL:ritroso]
- [limit statement] [SKILL:skill-name] ← from injected skill

## Hard limits — injected from active skills
For each ACTIVE skill:
### [skill-name] limits [SKILL:skill-name]
- [rule extracted verbatim from skill] [SKILL:skill-name]
- [rule extracted verbatim from skill] [SKILL:skill-name]

## Forbidden patterns (technical)
- [pattern] — why forbidden — [SKILL:name]

## Forbidden patterns (product)
- [pattern] — why forbidden — [SKILL:ritroso]

## Regulatory / legal
State explicitly: what regulations apply (GDPR, CCPA, HIPAA, PCI-DSS, etc.)
based on the data types this product handles.
- If regulated: list each regulation and the specific requirement it imposes
- If not regulated: "N/A — [specific reason: domain, data type, jurisdiction]"
  [SKILL:ritroso]

## Consistency check
State explicitly: do any limits in this file contradict 01_GOAL or 02_PRODUCT?
- If YES: flag as [GOAL-CONFLICT] and reference 11_INTERPOLATION.
- If NO: write "No contradictions detected between 08_LIMITS and 01_GOAL / 02_PRODUCT." [SKILL:ritroso]
```

---

### 09_AGENTS.md

```markdown
# 09 — Agents

> "Agent" = any human, team, or automated system with a decision-making role.
> Every role not explicitly named in the original prompt MUST be tagged [ASSUMED-NO-BASIS]
> and appear in 12_ASKED with the structured format.
> **Deduplication rule**: if the same person holds multiple roles, merge them into ONE role
> with combined responsibilities. Never list "Founder / Product owner" and "Founder / Builder"
> as separate entries — write one role "Founder" with all responsibilities enumerated.

## Decision matrix
| Decision type | Owner | Fallback owner | Escalation path |
|---------------|-------|----------------|-----------------|
| Architecture changes | [role] | [role] [ASSUMED-NO-BASIS if not in prompt] | [process] |
| Feature scope changes | [role] | [role] | [process] |
| Budget overrun >10% | [role] | [role] | [process] |
| Security incidents | [role] | [role] | [process] |
| Launch go/no-go | [role] | [role] | [process] |

## Roles

### [Role name] [ASSUMED-NO-BASIS if not named in prompt]
- **Responsibilities**: [concrete list — not "oversees X" but "approves X before Y happens"]
- **Decision authority**: [what this role can unilaterally decide]
- **Blocked by**: [what requires escalation]
- **Availability**: [full-time / part-time / on-call]

[Repeat for each role]

## Handoff protocol
<!-- How tasks flow between agents — each handoff must specify:
     From → To · Trigger condition · Required input format · Acceptance criteria · Rejection handling -->
| From | To | Trigger | Required input | Acceptance criteria | Rejection handling |
|------|----|---------|---------------|---------------------|-------------------|
| [role] | [role] | [event] | [format] | [condition] | [action] |

## Escalation paths
<!-- When an agent fails, is blocked, or exceeds its scope: who takes over?
     Must cover: agent failure, budget exceeded, ambiguous input, conflicting instructions -->
| Agent | Blocked/failed → escalates to | Via | Within |
|-------|------------------------------|-----|--------|
| [role] | [role or human] | [channel] | [time] |

## Capability assessment
<!-- Can each agent actually do what is asked with the declared resources? -->
| Agent | Task | Feasible? (YES/PARTIAL/NO) | Constraint | Mitigation |
|-------|------|---------------------------|------------|------------|
| [role] | [task] | [YES/PARTIAL/NO] | [constraint] | [mitigation] |

## Automated agents / systems
| System | What it decides autonomously | Human override required for |
|--------|------------------------------|----------------------------|
| [CI/CD pipeline] | [what it auto-does] | [what needs human approval] |

## Communication protocol
- Sync frequency: [daily standup / weekly review / async only]
- Decision log: [where decisions are recorded]
- Blocking escalation: [how a blocker gets raised and resolved]
```

---

### 10_ERROR.md

```markdown
# 10 — Error Scenarios

> Minimum 3 failure scenarios. Must include at least one human/organisational failure.
> **Every failure scenario MUST include: Probability, Impact, Detection, Recovery, Prevention.**
> Format: concrete, named, with all five fields present.

## Failure scenarios

### Failure 1 — [descriptive name] (Technical / Human / Market)
- **What happens**: [specific failure description — not "system crashes" but "X fails because Y"]
- **Trigger**: [what causes it]
- **Probability**: [low / medium / high] — [rationale]
- **Impact**: [who is affected, what they cannot do, financial/reputational cost]
- **Detection**: [how we know it happened — metric, alert, observation]
- **Recovery**: [step-by-step action — who does what in what order]
- **Prevention**: [what in 03_NEXT_STEPS or 08_LIMITS prevents this]
- **[ASSUMED-NO-BASIS] ref**: [name of assumption in 12_ASKED if applicable / none]

### Failure 2 — [descriptive name] (Technical / Human / Market)
[same structure — all 8 fields required]

### Failure 3 — [descriptive name] (Human / Organisational)
[same structure — MUST be a people/process failure, not a code failure]

### Failure 4 — [descriptive name] [if applicable]
[same structure]

### Failure 5 — [descriptive name] [if applicable]
[same structure]

## Risk register
| Scenario | Probability | Impact | P1 mitigation | Owner |
|----------|-------------|--------|---------------|-------|
| Failure 1 | [H/M/L] | [H/M/L] | [action] | [role] |
| Failure 2 | [H/M/L] | [H/M/L] | [action] | [role] |
| Failure 3 | [H/M/L] | [H/M/L] | [action] | [role] |
```

---

### 11_INTERPOLATION.md

```markdown
# 11 — Interpolation

> This file checks cross-file consistency.
> Every conflict found here must be resolved before PHASE 4 closes.
> If no conflict exists, write the explicit none-verification lines — do not leave them absent.

## Cross-file consistency checks

### 01_GOAL ↔ 08_LIMITS
[Do the limits prevent achieving the goals? State finding explicitly.]

### 02_PRODUCT ↔ 07_BUDGET
[Is the declared P1 feature set achievable with the declared budget? State finding.]

### 03_NEXT_STEPS ↔ 09_AGENTS
[Does every step in 03 have a named owner in 09? State any gaps.]

### 05_COMPONENTS ↔ 08_LIMITS
[Does any component in the stack violate a hard limit? State finding.]

### 06_PRICE ↔ 07_BUDGET
[Is revenue model consistent with build cost? State finding and reference break-even.]

## Detected conflicts
For each conflict found:
```
[GOAL-CONFLICT: {file A} vs {file B}]
Description: [what the conflict is]
Resolution: [how it was resolved, or "escalated to 12_ASKED: [item name]"]
```

## Skill conflicts
For each pair of active skills that cover overlapping areas:
```
[SKILL-CONFLICT: {skill-a} vs {skill-b}]
Overlap: [what they both address]
Resolution: [which rule takes precedence and why]
```

## Explicit none-verification (MANDATORY if no conflicts found)
```
[SKILL-CONFLICT: none verified]
[GOAL-CONFLICT: none verified]
```
**Do not omit these lines.** Their absence signals "forgotten to check", not "clean".
```

---

### 12_ASKED.md

```markdown
# 12 — Asked

> Open questions and assumptions that affect the generation.
> **Every [ASSUMED-NO-BASIS] MUST use the structured 4-field format below.**
> Free-text consequences are not accepted.

## Open questions ([INFERRED-FROM-TEXT])
Answer would improve precision but is not blocking.

- [INFERRED-FROM-TEXT] {question}
  → Currently assumed: {what was assumed}
  → Files affected: {list}
  → Impact if wrong: {low / medium / high}

## Assumptions without basis ([ASSUMED-NO-BASIS])
Answer would change the output. Structured 4-field format required.

- [ASSUMED-NO-BASIS] {claim}
  → Files affected: {comma-separated list of Ritroso file names}
  → Scope impact: {low / medium / high}
  → Entry in 10_ERROR: {exact scenario name as written in 10_ERROR}

## Dependency warning
State explicitly: "No P1 step in 03_NEXT_STEPS depends on an unresolved ASSUMED-NO-BASIS item."
OR list every P1 step that is blocked by an open assumption, and name the blocker.

## Decisions deferred from this generation
| Decision | Deferred to | Reason | Blocking? |
|----------|-------------|--------|-----------|
| [decision] | [phase/file/owner] | [reason] | [yes/no] |
```

---

### 00_INDEX.md

> **GENERATE THIS FILE LAST.** It summarizes everything above.

```markdown
---
name: "00_INDEX.md"
description: "Master navigation and verification surface for {project_name}. Contains the complete Skill Discovery Log, the per-file Panel of Agents table, and the final Verification Status. Read this file first to understand the full file set status."
---

# 00 — Index — {project_name}
Domain: {domain_slug} · Generated: {date} · Ritroso v6.1
Folder: new-ideas/{domain_slug}/{project_name_slug}/

## Project Overview
One paragraph: project name, domain, primary goal, target user, P1 scope boundary.

## File Map
| # | File | Purpose | Status |
|---|------|---------|--------|
| 01 | [[01_GOAL]] | Why it exists | FINAL / DRAFT |
| 02 | [[02_PRODUCT]] | What it does — user flows | FINAL / DRAFT |
| 03 | [[03_NEXT_STEPS]] | Roadmap with owners and sign-off | FINAL / DRAFT |
| 04 | [[04_ELEMENTS]] | All required elements | FINAL / DRAFT |
| 05 | [[05_COMPONENTS]] | Tech stack and contracts | FINAL / DRAFT |
| 06 | [[06_PRICE]] | Pricing strategy | FINAL / DRAFT |
| 07 | [[07_BUDGET]] | Build cost + contingency | FINAL / DRAFT |
| 08 | [[08_LIMITS]] | Hard limits with [SKILL:name] tags | FINAL / DRAFT |
| 09 | [[09_AGENTS]] | Decision owners and fallbacks | FINAL / DRAFT |
| 10 | [[10_ERROR]] | Failure scenarios | FINAL / DRAFT |
| 11 | [[11_INTERPOLATION]] | Cross-file conflicts | FINAL / DRAFT |
| 12 | [[12_ASKED]] | Open questions and assumptions | FINAL / DRAFT |

## Skill Discovery Log
- Method used: [1-Context / 2-Direct path / 3-Grep / C-Remote fetch / 4-Inference]
- OS detected: [Windows / macOS / Linux / Unknown]
- Paths scanned:
  - `~/.claude/skills/` → [found N folders / access denied / not found]
  - `<project>/.claude/skills/` → [found N folders / not found]
  - [all paths checked, one line each]
- Grep fallback: [ran / not needed / access denied]
- Remote fetch attempts (Method C):
  - `[skill-name]` → [URL] → [200 OK: fetched / 404 / timeout / skipped]
- Agent install attempts:
  - `[skill-name]` → [Method B / Method A] → [success: path / failed: reason]
- Custom skills found: [N user-defined SKILL.md files / none]

## Skill Stack — Active
| Skill | Source | Category | Injected Into | Rules Injected |
|-------|--------|----------|---------------|----------------|
| [name] | [local path / "context" / "remote-fetch: URL"] | [category] | [file list] | [N rules] |

## Skill Stack — Recommended (ABSENT)
| Skill | Category | What it would inject | Install |
|-------|----------|---------------------|---------|
| [name] | [cat] | [files + content] | [Method B command] |

## Panel of Agents — Validation Log
| File | ARCHITECT | DESIGNER | PRAGMATIST | CRITIC | Status |
|------|-----------|----------|------------|--------|--------|
| 01_GOAL.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 02_PRODUCT.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 03_NEXT_STEPS.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 04_ELEMENTS.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 05_COMPONENTS.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 06_PRICE.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 07_BUDGET.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 08_LIMITS.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 09_AGENTS.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 10_ERROR.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 11_INTERPOLATION.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |
| 12_ASKED.md | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | ✓ / ✗ | CLOSED |

## Verification Status
- All 13 files: [ ] YES / [ ] NO
- 03_NEXT_STEPS present: [ ] YES / [ ] NO
- Open BLOCKs: [ ] NONE / [ ] LIST THEM
- 08_LIMITS tagged limits: [ ] ALL TAGGED / [ ] UNTAGGED → LIST
- 11_INTERPOLATION none-verified lines: [ ] PRESENT / [ ] MISSING
- 12_ASKED 4-field format: [ ] ALL STRUCTURED / [ ] NON-STRUCTURED → LIST
- Goal-Pricing consistency: [ ] CONSISTENT / [ ] INCONSISTENT → LIST
- Budget alignment: [ ] EXPLAINED / [ ] UNEXPLAINED → LIST
- Role deduplication: [ ] CLEAN / [ ] DUPLICATES → LIST
- Encoding validation: [ ] CLEAN / [ ] CORRUPTED → LIST
- Gate: [ ] CLOSED (RITROSO-VERIFIED) / [ ] OPEN (reason: ...)
```

---

## Install Methods

### Method 0 — Paste inline (no install, works everywhere)

Copy the entire content of this file and paste it as your first message in the chat with your model.
The model will treat it as an active skill immediately.

Raw URL for fetch:
```
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md
```

### Method A — Full install (recommended for repeated use)

```bash
# macOS / Linux
git clone https://github.com/Domenico0976/Ritroso.md.git
mkdir -p ~/.claude/skills/Ritroso
cp Ritroso.md/SKILL.md ~/.claude/skills/Ritroso/SKILL.md
cp Ritroso.md/prompts/file-set-templates/*.md ~/.claude/skills/Ritroso/templates/
```

```powershell
# Windows PowerShell
git clone https://github.com/Domenico0976/Ritroso.md.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\Ritroso"
Copy-Item Ritroso.md\SKILL.md "$env:USERPROFILE\.claude\skills\Ritroso\SKILL.md"
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\Ritroso\templates"
Copy-Item Ritroso.md\prompts\file-set-templates\*.md "$env:USERPROFILE\.claude\skills\Ritroso\templates\"
```

### npx (markdownskill.com)

```bash
npx skills add Domenico0976/Ritroso.md
```

---

## Philosophy

> *"The model must be wrong before it can be right."*

Negative verification is not destructive — it is the mechanism that transforms a mediocre output into a coherent one. The inference loop adds a second layer: **coherence is built before generation, not just verified after**.

> **Mantra**: *create the most reliable instructions possible.*
Every phase, every tag, every check exists to serve this single goal.
