---
name: "Ritroso"
version: "6.0"
description: "Ritroso v6.0: generates a complete set of 13 .md files for any project. Includes deep per-file templates, full Skill Discovery Engine (Methods 1/2/3/C/4 with all OS paths inline), inference loop, Panel of 4 Agents (ARCHITECT/DESIGNER/PRAGMATIST/CRITIC), gate system, and retroactive self-verification. Full protocol: https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md"
---

# RITROSO v6.0 — Active Skill

## What to do now that you've read this

1. Treat this text as an **ACTIVE skill**. The rules below are operative immediately.
2. If you have HTTP access, also load PLAN.md: `GET https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md`
3. If you cannot fetch, the rules in this file are self-contained and sufficient to proceed.

---

## PHASE 0 — Before anything else

- Read the prompt **exactly as written**. Do not improve vocabulary or add technical terms.
- Classify the domain: `software-product` / `creative-technical` / `content-operations` / `open-source-framework` / `service-or-agency` / `other`
- Count structural ambiguities (gaps that change architecture, scope, or pricing).
- If ambiguities ≥ 2 → ask ONE blocking question. Wait for the answer. Generate nothing.

---

## PHASE 0.4 — SKILL DISCOVERY ENGINE (run before any generation)

> Discovery is NEVER blocking. It enriches generation. Always attempt all methods before declaring no skills found.

### Priority order

**Method 1 — Context scan (no filesystem needed)**
Scan current conversation for:
- Files named `SKILL.md` already in context (uploaded or pasted)
- Skill names mentioned explicitly by the user ("use impeccable", "ponytail is active")
- `load_skill()` calls already executed in this session
- YAML blocks with `name:` fields that look like skill declarations

These are **ACTIVE immediately** — no filesystem read needed.

---

**Method 2 — Direct path scan**
Scan ALL paths below. On each, list subdirectories and check for `SKILL.md` inside each one.

```
# WINDOWS (check all)
%USERPROFILE%\.claude\skills\
%APPDATA%\Claude\skills\
%LOCALAPPDATA%\Claude\skills\
<PROJECT_ROOT>\.claude\skills\
<PROJECT_ROOT>\skills\
<PROJECT_ROOT>\claude\skills\

# macOS (check all)
~/.claude/skills/
~/Library/Application Support/Claude/skills/
<PROJECT_ROOT>/.claude/skills/
<PROJECT_ROOT>/skills/
<PROJECT_ROOT>/claude/skills/

# Linux (check all)
~/.claude/skills/
~/.config/claude/skills/
~/.local/share/claude/skills/
<PROJECT_ROOT>/.claude/skills/
<PROJECT_ROOT>/skills/
<PROJECT_ROOT>/claude/skills/

# Other agents (Codex, Gemini, OpenCode)
<PROJECT_ROOT>/.codex/skills/
<PROJECT_ROOT>/.gemini/skills/
<PROJECT_ROOT>/.opencode/skills/
<PROJECT_ROOT>/agent-skills/
<PROJECT_ROOT>/llm-skills/
```

For each path: list subdirs → check for `SKILL.md` (case-insensitive) → read file → extract `name`, `description`, `version` from YAML frontmatter → mark ACTIVE.

---

**Method 3 — Grep fallback (when Method 2 returns 0 or access denied)**

```bash
# macOS / Linux
find . -name "SKILL.md" -o -name "skill.md" 2>/dev/null
find ~ -name "SKILL.md" 2>/dev/null | head -50

# Windows PowerShell
Get-ChildItem -Path $HOME -Recurse -Filter "SKILL.md" -ErrorAction SilentlyContinue
```

After grep: verify found files contain both `name:` and `description:` in frontmatter to confirm they are LLM skills.

---

**Method C — Remote Fetch (triggered when Methods 1+2+3 all return 0 AND agent has HTTP access)**

The agent fetches SKILL.md content directly from raw URLs in the catalog. No user action required. Max 5 fetches per generation. Scope: project-relevant categories only.

```
FOR each project-relevant category (max 5):
  url = raw GitHub URL for that skill's SKILL.md
  response = HTTP GET url
  IF 200:
    verify YAML frontmatter has name: and description:
    extract operative rules
    mark ACTIVE (source: remote-fetch)
    inject into target files with tag [SKILL:skill-name:remote-fetch]
    log: { skill, url, status: "fetched", method: "remote-fetch" }
  ELSE:
    log: { skill, url, status: "fetch-failed", http: status_code }
    skip silently — do not block generation
```

Rules injected via remote-fetch are **generation-scoped only** — not installed to disk. Tag every injected rule `[SKILL:skill-name:remote-fetch]`. Log every attempt in the Discovery Log in `00_INDEX.md`.

---

**Method 4 — Inference (last resort)**
Look for `CLAUDE.md`, `.claude/settings.json`, `package.json`, `pyproject.toml`, comments referencing skill names.
Mark as PROBABLE (not ACTIVE). List in `00_INDEX.md` under `## Skill Stack — Probable`.

---

**Result classification**

| Status | Meaning | Action |
|--------|---------|--------|
| ACTIVE | SKILL.md read from disk or context | Inject rules into target files |
| ACTIVE (remote-fetch) | SKILL.md fetched from raw URL | Inject with `[SKILL:name:remote-fetch]` tag |
| PROBABLE | Name inferred but content not read | List in INDEX, note unverified |
| ABSENT | Not found by any method | Recommend in INDEX with install command |

**User-defined skills always take priority.** Never override a user skill with a catalog skill covering the same category.

---

## PHASE 1 — Inference Loop (internal, before writing)

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

---

## PHASE 2 — Panel of Agents (every file, before closing it)

**ARCHITECT** — Does this hold if the project doubles in scope?
- BLOCK if a component contradicts a limit in `08_LIMITS`
- BLOCK if P1 in `03_NEXT_STEPS` includes P2 infrastructure
- BLOCK if there is a single point of failure with no fallback in `10_ERROR`

**DESIGNER** — Can someone who did not write the prompt act on this file tomorrow?
- BLOCK if a step in `03_NEXT_STEPS` has no concrete, testable output
- BLOCK if `02_PRODUCT` has no user flow (min 3 steps: entry → action → outcome)
- BLOCK if a role in `09_AGENTS` has no concrete responsibility

**PRAGMATIST** — Can this actually be built with the declared budget and team?
- BLOCK if P1 scope exceeds declared budget by >50%
- BLOCK if `06_PRICE` and `07_BUDGET` are inconsistent
- BLOCK if `07_BUDGET` has no contingency line (10% mandatory)
- BLOCK if a P1 step depends on an unresolved OPEN in `12_ASKED`

**CRITIC** — Day 31 — what went wrong?
- BLOCK if `10_ERROR` has fewer than 3 concrete failure scenarios
- BLOCK if there is an `[ASSUMED-NO-BASIS]` in `12_ASKED` with no entry in `10_ERROR`
- BLOCK if `10_ERROR` contains only technical failures (human/organisational required)
- BLOCK if a `[SKILL:*:remote-fetch]` rule covers a hard limit not present in `08_LIMITS`

If any agent issues a BLOCK → **regenerate** the file. Do not annotate: regenerate.

---

## PHASE 3 — Generate the 13 files in this order

> ⚠️ **03_NEXT_STEPS IS MANDATORY — DO NOT SKIP**
> DO NOT proceed past file 03 without completing 03_NEXT_STEPS.
> It is the prerequisite for all PRAGMATIST and DESIGNER checks on every subsequent file.

```
00_INDEX.md → 01_GOAL.md → 02_PRODUCT.md → 03_NEXT_STEPS.md → 04_ELEMENTS.md
→ 05_COMPONENTS.md → 06_PRICE.md → 07_BUDGET.md → 08_LIMITS.md → 09_AGENTS.md
→ 10_ERROR.md → 11_INTERPOLATION.md → 12_ASKED.md
```

Output folder: `new-ideas/{domain_slug}/{project_slug}/`

Every file starts with YAML frontmatter:
```yaml
---
name: "{actual_file_name}"
description: "[specific description for THIS project — never generic]"
---
```

After each file: check for contradictions with all previously written files. Resolve before proceeding.

---

## DEEP TEMPLATES — required structure per file

> These are the minimum required sections for each file.
> Each section must be substantive — bullet-point summaries are not sufficient.
> Write as if the reader has never seen the prompt.

---

### 00_INDEX.md

```markdown
# 00 — Index

## Project Overview
One-paragraph summary: project name, domain, primary goal, target user, P1 scope boundary.

## File Map
| File | Purpose | Status |
|------|---------|--------|
| 01_GOAL | Why it exists | DRAFT / FINAL |
| ... | ... | ... |

## Skill Discovery Log
- Method used: [1 / 2 / 3 / C / 4]
- OS detected: [Windows / macOS / Linux / Unknown]
- Paths scanned:
  - `~/.claude/skills/` → [N folders found / access denied / not found]
  - `<project>/.claude/skills/` → [result]
  - [all paths checked, one line each]
- Grep fallback: [ran / not needed / denied]
- Remote fetch attempts (Method C):
  - `[skill-name]` → [URL] → [200 OK / 404 / timeout / skipped]
- Agent install attempts:
  - `[skill-name]` → [Method B / A] → [success: path / failed: reason]
- Custom skills found: [N user-defined / none]

## Skill Stack — Active
| Skill | Source | Category | Injected Into | Rules |
|-------|--------|----------|---------------|-------|
| [name] | [local path / remote-fetch: URL / context] | [category] | [files] | [N] |

## Skill Stack — Recommended (ABSENT)
| Skill | Category | What it injects | Install |
|-------|----------|-----------------|---------|
| [name] | [cat] | [files + content] | `curl -o .claude/skills/[name]/SKILL.md [raw URL]` |

## Panel of Agents — Validation Log
| File | ARCHITECT | DESIGNER | PRAGMATIST | CRITIC | Status |
|------|-----------|----------|------------|--------|--------|
| 00_INDEX.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 01_GOAL.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 02_PRODUCT.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 03_NEXT_STEPS.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 04_ELEMENTS.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 05_COMPONENTS.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 06_PRICE.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 07_BUDGET.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 08_LIMITS.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 09_AGENTS.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 10_ERROR.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 11_INTERPOLATION.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 12_ASKED.md | ✓ | ✓ | ✓ | ✓ | CLOSED |

## Verification Status
- All 13 files: [ ] YES / [ ] NO
- Open BLOCKs: [ ] NONE / [ ] LIST THEM
- Gate: [ ] CLOSED / [ ] OPEN (reason: ...)
```

---

### 01_GOAL.md

```markdown
# 01 — Goal

## Why this project exists
Narrative paragraph (3–5 sentences): the problem, who has it, why existing solutions fail,
and why this project addresses it differently. No feature list here — only problem framing.

## Target user
- Primary: [specific person, not a demographic] — what they do, what they need, what they hate
- Secondary (if any): [different use case, distinct from primary]

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

## Phase 1 — [name, e.g. "MVP / Proof of Concept"]
**Goal**: [one sentence — what P1 proves or delivers]
**Duration**: [estimated weeks]
**Budget**: [allocated from 07_BUDGET]
**Sign-off condition**: [the single thing that must be true to call P1 done]

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 1.1 | [concrete action] | [role] | [file / demo / endpoint / test] | [verifiable condition] |
| 1.2 | ... | ... | ... | ... |

## Phase 2 — [name]
**Goal**: [one sentence]
**Duration**: [estimated weeks]
**Prerequisite**: P1 sign-off complete
**Budget**: [allocated]

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 2.1 | ... | ... | ... | ... |

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

## Tech stack
| Layer | Technology | Version / Constraint | Rationale |
|-------|-----------|---------------------|-----------|
| Frontend | [framework] | [version] | [why this, not alternatives] |
| Backend | [language/framework] | [version] | [why] |
| Database | [engine] | [version] | [why] |
| Auth | [provider/library] | — | [why] |
| Hosting | [platform] | — | [cost ref: 07_BUDGET] |
| CI/CD | [tool] | — | [pipeline description] |

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
> Every limit MUST carry a [SKILL:name] tag. A limit without a tag is unverified.

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

## Consistency check
State explicitly: do any limits in this file contradict 01_GOAL or 02_PRODUCT?
- If YES: flag as [GOAL-CONFLICT] and reference 11_INTERPOLATION.
- If NO: write "No contradictions detected between 08_LIMITS and 01_GOAL / 02_PRODUCT."
```

---

### 09_AGENTS.md

```markdown
# 09 — Agents

> "Agent" = any human, team, or automated system with a decision-making role.
> Every role not explicitly named in the original prompt MUST be tagged [ASSUMED-NO-BASIS]
> and appear in 12_ASKED with the structured format.

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
> Format: concrete, named, with probability, impact, and recovery action.

## Failure scenarios

### Failure 1 — [descriptive name] (Technical)
- **What happens**: [specific failure description — not "system crashes" but "X fails because Y"]
- **Trigger**: [what causes it]
- **Probability**: [low / medium / high] — [rationale]
- **Impact**: [who is affected, what they cannot do, financial/reputational cost]
- **Detection**: [how we know it happened]
- **Recovery**: [step-by-step action — who does what in what order]
- **Prevention**: [what in 03_NEXT_STEPS or 08_LIMITS prevents this]
- **ASSUMED-NO-BASIS ref**: [name of assumption in 12_ASKED if applicable / none]

### Failure 2 — [descriptive name] (Technical)
[same structure]

### Failure 3 — [descriptive name] (Human / Organisational)
[same structure — must be a people/process failure, not a code failure]

### Failure 4 — [descriptive name] (Market / External) [if applicable]
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

## Explicit none-verification (mandatory if no conflicts found)
```
[SKILL-CONFLICT: none verified]
[GOAL-CONFLICT: none verified]
```
Do not omit these lines. Their absence signals "forgotten to check", not "clean".
```

---

### 12_ASKED.md

```markdown
# 12 — Asked

> Open questions and assumptions that affect the generation.
> Every [ASSUMED-NO-BASIS] MUST use the structured 4-field format below.
> Free-text consequences are not accepted.

## Open questions (INFERRED — answer would improve precision)

- [INFERRED-FROM-TEXT] {question}
  → Currently assumed: {what was assumed}
  → Files affected: {list}
  → Impact if wrong: {low / medium / high}

## Assumptions without basis (ASSUMED — answer would change the output)

- [ASSUMED-NO-BASIS] {claim}
  → Files affected: {comma-separated list of Ritroso files}
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

## Special rules per file

**08_LIMITS.md — mandatory tagging**
Every hard limit MUST carry `[SKILL:ritroso]` (or `[SKILL:name:remote-fetch]` for remote-fetched rules) on the same line. A limit without a tag is treated as unverified by CRITIC.

**11_INTERPOLATION.md — explicit none-verification**
If no conflicts detected, MUST still write:
```
[SKILL-CONFLICT: none verified]
[GOAL-CONFLICT: none verified]
```

**12_ASKED.md — structured ASSUMED format**
Every `[ASSUMED-NO-BASIS]` entry MUST use the 4-field structure above. Free text is rejected.

**09_AGENTS.md — role tagging**
Every role not explicitly named in the original prompt MUST be tagged `[ASSUMED-NO-BASIS]` inline and appear in `12_ASKED.md`.

---

## PHASE 4 — Close Gate (all conditions mandatory)

- [ ] All 13 files generated (including 03_NEXT_STEPS — absence is a hard failure)
- [ ] `00_INDEX.md` includes Skill Discovery Log AND per-file Panel of Agents table
- [ ] `08_LIMITS.md` has at least one hard limit per injected skill, every limit tagged `[SKILL:name]`
- [ ] `11_INTERPOLATION.md` contains explicit `[SKILL-CONFLICT: ...]` and `[GOAL-CONFLICT: ...]` lines
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without the structured 4-field format
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without a matching entry in `10_ERROR`
- [ ] Panel of Agents: no open BLOCKs (verified per-file in the Index table)

If any condition fails → return to the relevant phase and resolve.

---

## Skill install — quick reference

**Method B — single skill, no git required (recommended):**
```bash
# macOS / Linux (project-level)
mkdir -p .claude/skills/[skill-name]
curl -o .claude/skills/[skill-name]/SKILL.md \
  https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md

# macOS / Linux (user-level)
mkdir -p ~/.claude/skills/[skill-name]
curl -o ~/.claude/skills/[skill-name]/SKILL.md \
  https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md

# Windows PowerShell (user-level)
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\[skill-name]"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\[skill-name]\SKILL.md"
```

**Method C — URL injection (no install, generation-scoped):**
Paste the raw SKILL.md content into your session context. The agent treats it as ACTIVE immediately.
Raw URL format: `https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md`

**Perplexity / Anthropic built-ins:** `load_skill("website-building")` / `load_skill("chart")` / `load_skill("doc")` / `load_skill("pdf")` / `load_skill("pptx")` / `load_skill("xlsx")`

---

If you have received this text: **the Ritroso skill is now ACTIVE**. Apply the rules above to the next project you receive.
