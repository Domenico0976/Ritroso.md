---
name: "Ritroso"
version: "5"
description: "Ritroso execution skill v5. Contextual reasoning protocol for LLMs. Generates complete 13-file .md sets for any project (software, creative, content, open-source, service). Includes inference loop, Panel of 4 Agents (ARCHITECT / DESIGNER / PRAGMATIST / CRITIC), cross-agent attack protocols, gate system, Skill Discovery & Injection Engine (5 discovery methods including remote-fetch), and retroactive self-verification. PLAN.md is the companion execution protocol — load it alongside this file, or the agent will fetch it automatically if HTTP access is available."
applies_to: "All Ritroso file-set generations"
plan_md_raw_url: "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md"
skill_md_raw_url: "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md"
repo: "https://github.com/Domenico0976/Ritroso.md"
---

# RITROSO — Contextual Reasoning Skill v5

> Contextual reasoning skill for LLMs with multimodal generation of complete `.md` file sets,
> inference loop, Panel-of-Agents validation, cross-agent attack protocols, and retroactive self-verification.

---

## ⚠️ MANDATORY: Load PLAN.md before executing

This file (SKILL.md) defines **what** Ritroso does and **what** each output file must contain.
PLAN.md defines **when** and **how** — execution phases, gates, and the Skill Discovery Engine.
They are complementary and both required.

**If PLAN.md is not already in context**, do one of the following (in priority order):

1. **Fetch it automatically** (if HTTP access is available):
   ```
   GET https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
   ```
   Treat the response as an active protocol file immediately.

2. **Paste it manually**: open the URL above in a browser, copy the full content, paste into this chat.

3. **Install permanently** (so it's always available):
   ```bash
   # macOS / Linux
   mkdir -p ~/.claude/skills/Ritroso
   curl -o ~/.claude/skills/Ritroso/SKILL.md \
     https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md
   curl -o ~/.claude/skills/Ritroso/PLAN.md \
     https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md

   # Windows PowerShell
   New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\Ritroso"
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md" `
     -OutFile "$env:USERPROFILE\.claude\skills\Ritroso\SKILL.md"
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md" `
     -OutFile "$env:USERPROFILE\.claude\skills\Ritroso\PLAN.md"
   ```

> If PLAN.md cannot be fetched or located: stop and ask the user to provide it before continuing.

---

## What Ritroso Generates

For every input prompt, Ritroso produces a folder with **13 structured `.md` files**:

| # | File | Contains |
|---|------|----------|
| 00 | `00_INDEX.md` | Navigation hub, verification status, Skill Discovery Log, Skill Stack |
| 01 | `01_GOAL.md` | Problem statement, success criteria, stakeholder map |
| 02 | `02_PRODUCT.md` | Product spec, user flows (P1/P2), feature scope, system requirements |
| 03 | `03_NEXT_STEPS.md` | Phase-by-phase roadmap with owners, outputs, sign-off conditions |
| 04 | `04_ELEMENTS.md` | All required elements: Critical / Important / Optional |
| 05 | `05_COMPONENTS.md` | Full tech stack, component contracts, injected skill rules |
| 06 | `06_PRICE.md` | Pricing strategy, tiers, value framing |
| 07 | `07_BUDGET.md` | Build cost, infrastructure cost, contingency (10% mandatory) |
| 08 | `08_LIMITS.md` | Hard limits, injected skill hard limits, forbidden patterns |
| 09 | `09_AGENTS.md` | Decision owners, roles, responsibilities, fallback owners |
| 10 | `10_ERROR.md` | ≥3 concrete failure scenarios with recovery actions |
| 11 | `11_INTERPOLATION.md` | Cross-file contradictions, GOAL-CONFLICT, SKILL-CONFLICT |
| 12 | `12_ASKED.md` | All open questions tagged [INFERRED-FROM-TEXT] or [ASSUMED-NO-BASIS] |

Output folder: `new-ideas/{domain_slug}/{project_name_slug}/`

---

## Execution Order

Execute these steps in order. Do not skip or reorder.

### STEP 1 — PLAN.md Phase 0 (Prompt Intake, Classification & Skill Discovery)
- Read the prompt exactly as written — do not upgrade vocabulary, do not infer intent
- Classify project domain (PLAN.md §0.2)
- Run Skill Discovery Engine (PLAN.md §0.4.0) — all 5 methods in priority order:
  1. Context scan (files/mentions already in session)
  2. Direct path scan (known skill paths on all OS)
  3. Grep fallback
  4. **Method C — Remote Fetch** (auto-fetch raw URLs from catalog if methods 1-3 return 0)
  5. Inference from project structure
- Count structural ambiguities (PLAN.md §0.3)
- If ambiguities ≥ 2 → GATE 0: ask ONE blocking question, stop, wait
- Only proceed when ambiguities < 2

### STEP 2 — PLAN.md Phase 1 (Inference Loop)
- Run all inter-file inference questions **internally** before writing any file
- Tag every assumption: `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]`
- Check goal compatibility — flag `[GOAL-CONFLICT]` before proceeding
- Active skill rules from §0.4 are operative from this moment

### STEP 3 — Create output folder
`new-ideas/{domain_slug}/{project_name_slug}/`

### STEP 4 — Generate all 13 files with frontmatter
- Generate in the order defined in PLAN.md Phase 3 (00 through 12)
- Every file starts with YAML frontmatter (Dynamic File Frontmatter Rule below)
- Apply all Project Rules from PLAN.md relevant to that file
- Apply all injected skill rules tagged `[SKILL:*]`
- After each file: run the **full Panel of Agents** (4 agents)
  - BLOCK → GATE 2: regenerate the file
  - NOTE → log in file, continue

### STEP 5 — PLAN.md Phase 3 (Negative Verification)
- Cross-file attack: goal / architecture / roadmap / assumption
- New BLOCK-level finding → GATE 3: regenerate affected files
- Add verification findings to `11_INTERPOLATION.md` and `03_NEXT_STEPS.md`

### STEP 6 — PLAN.md Phase 4 (Close Gate)
- Write `00_INDEX.md` last
- Populate Verification Status block with actual results
- Mark `RITROSO-VERIFIED` only if all Phase 4 conditions pass

---

## Dynamic File Frontmatter Rule

Every generated file MUST begin with:

```yaml
---
name: "{real_file_name}"
description: "Dynamic agent-facing description built from the file objective, current project
context, inferred limits, declared constraints, anti-patterns, and intended outcome. Must explain
how to read the file, what reasoning approach to prioritize, what limits matter most, and what to
do if context is incomplete. Must reference any active [GOAL-CONFLICT] or [ASSUMED-NO-BASIS]
items relevant to this file. Never generic when context exists."
---
```

**Constraints**:
- `name` must exactly match the filename
- `description` must name the specific project, domain, and key constraints
- Never generic — must reference the actual project
- If context is weak: instruct the agent to rely on linked files, not hallucinate

---

## Assumption Tagging Rule

Every assumption must be tagged:
- `[INFERRED-FROM-TEXT]` — supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support

Every `[ASSUMED-NO-BASIS]` item MUST:
1. Appear in `12_ASKED.md` with tag and justification
2. Have a corresponding risk entry in `10_ERROR.md`
3. List which files would need rewriting if the assumption is wrong

---

## Goal Conflict Rule

If two or more goals in `01_GOAL.md` are in tension:
1. Tag: `[GOAL-CONFLICT: GOAL-A vs GOAL-B]`
2. Document in `11_INTERPOLATION.md`: what conflicts, why, resolution options
3. Do NOT silently resolve by choosing one goal
4. Surface in `12_ASKED.md` as a structural question

---

## Panel of Agents — Summary

Every file is reviewed by all 4 agents before being closed.
Agents ATTACK the draft — they look for the most damaging problem they can find.
Injected skill rules (`[SKILL:*]`) are binding project rules for all agents.
BLOCK → file regenerated. NOTE → logged, continue.

For full agent specifications (interrogation questions, BLOCK/NOTE conditions, cross-referral
targets), see PLAN.md Phase 2.

### 🏛 ARCHITECT
**Mandate**: Structural integrity across time, scale, and change.
**Key question**: Does this hold if the project doubles in scope?

BLOCKS:
- Component contradicts a limit in `08_LIMITS.md`
- P1 in `03_NEXT_STEPS.md` includes P2 infrastructure
- Security skill injected but required P1 component missing from `04_ELEMENTS.md`
- Single point of failure with no fallback in `10_ERROR.md`

### 🎨 DESIGNER
**Mandate**: Usability and actionability for humans and agents consuming the files.
**Key question**: Can someone who didn't write this prompt act on this file tomorrow?

BLOCKS:
- Step in `03_NEXT_STEPS.md` has no concrete, testable output
- `02_PRODUCT.md` has no user flow (minimum 3 steps)
- Role in `09_AGENTS.md` has no concrete responsibility or output artifact
- Active `[SKILL:ux-ui-pro-max]` or `[SKILL:brand-guidelines]` rule violated

### ⚙️ PRAGMATIST
**Mandate**: Economic and operational feasibility.
**Key question**: Can this actually ship with the declared budget and team?

BLOCKS:
- P1 scope exceeds declared budget by >50%
- Component requires expertise not listed in `09_AGENTS.md`
- `06_PRICE.md` and `07_BUDGET.md` are inconsistent
- P1 step depends on an unresolved OPEN in `12_ASKED.md`
- `07_BUDGET.md` has no contingency line

### 🔒 CRITIC
**Mandate**: Find the most plausible near-term failure that kills the project.
**Key question**: It's day 31 — what went wrong?

BLOCKS:
- `10_ERROR.md` has fewer than 3 concrete failure scenarios
- Security skill injected but no security owner in `09_AGENTS.md`
- `[SKILL:*:remote-fetch]` rule covers a hard limit not in `08_LIMITS.md`
- `[ASSUMED-NO-BASIS]` item in `12_ASKED.md` with no risk entry in `10_ERROR.md`
- `10_ERROR.md` has only technical failures (no human/process/organizational failures)

---

## Skill Discovery Engine — Quick Reference

Full specification in PLAN.md §0.4.0.

| Method | Trigger | Action |
|--------|---------|--------|
| 1 — Context scan | Always first | Check session for SKILL.md files, mentions, load_skill() calls |
| 2 — Direct path scan | After Method 1 | Scan all known paths on Windows / macOS / Linux |
| 3 — Grep fallback | If Method 2 returns 0 | Run find / Get-ChildItem recursively from project root and home |
| C — Remote fetch (v1.4) | If Methods 1-3 return 0 | Auto-fetch raw URLs from catalog, inject as ACTIVE (remote-fetch), log |
| 4 — Inference | If all above return 0 | Check config files, package.json, import statements for skill names |

**Remote-fetch rules**: max 5 fetches per run, project-relevant categories only, all attempts
logged in `00_INDEX.md` Discovery Log, rules tagged `[SKILL:name:remote-fetch]`, no disk writes.

**Install methods (for permanent installation)**:

```bash
# Method A — clone full catalog (macOS/Linux)
git clone https://github.com/Prat011/awesome-llm-skills /tmp/awesome-llm-skills
cp -r /tmp/awesome-llm-skills/[skill-name] ~/.claude/skills/[skill-name]

# Method B — single skill curl (macOS/Linux, preferred)
mkdir -p ~/.claude/skills/[skill-name]
curl -o ~/.claude/skills/[skill-name]/SKILL.md \
  https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md

# Method B — single skill (Windows PowerShell)
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\[skill-name]"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\[skill-name]\SKILL.md"

# Method C — paste raw URL into chat (no install, generation-scoped)
# Open: https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md
# Copy content → paste into chat → agent treats as ACTIVE immediately
```

---

## Skill Categories Covered

13 injection categories with curated skill recommendations:

| Category | Key Skills | Injects Into |
|----------|-----------|-------------|
| `code-quality` | impeccable, simplify, code-reviewer | 05, 08, 03, 10 |
| `ui-ux-design` | ux-ui-pro-max, website-building, frontend-design | 02, 04, 05, 08, 09, 10 |
| `frontend-framework` | react-component-generator, nextjs-patterns, tailwind-converter | 05, 08, 03 |
| `api-backend` | api-design, auth-flow, crud-generator, realtime-sync | 04, 05, 08, 10 |
| `brand-styleguide` | brand-guidelines, ponytail | 02, 05, 08, 09 |
| `copywriting-content` | content-research-writer, brainstorming | 01, 02, 06, 08, 10 |
| `marketing-growth` | lead-research-assistant, competitive-ads-extractor | 01, 02, 03, 06, 07, 10, 12 |
| `security-defensive` | webapp-testing, llm-sast-scanner, auth-flow | 03, 04, 05, 08, 09, 10 |
| `security-offensive` | resemble-detect, computer-forensics, ffuf-web-fuzzing | 08, 10, 11, 03 |
| `testing-qa` | test-driven-development, tdd-guide, webapp-testing | 03, 05, 10 |
| `data-analytics` | csv-data-summarizer, d3js-visualization, postgres | 03, 04, 05, 08, 10 |
| `document-writing` | doc (built-in), pdf (built-in), markdown-to-epub | 01, 03, 09 |
| `financial-spreadsheet` | xlsx (built-in), invoice-organizer | 06, 07 |
| `presentation-slides` | pptx (built-in) | 01, 02 |
| `data-visualization` | chart (built-in), d3js-visualization | 02, 05, 08 |
| `ai-agents-mcp` | mcp-builder, skill-creator, maestro-orchestrate | 05, 08, 09 |
| `git-devops` | git-pushing, using-git-worktrees, deploy-checklist | 03, 08, 09 |
| `productivity-automation` | file-organizer, notion-spec-to-implementation | 03, 09 |

Full injection rules per category (what exactly gets injected into each target file): PLAN.md §0.4.2.

---

## Skill Discovery Resources

- [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) — primary curated list
- [awesomeclaude.ai/awesome-claude-skills](https://awesomeclaude.ai/awesome-claude-skills) — 204+ skills browsable by category
- [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) — framework-specific skills
- [github.com/VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) — 1000+ agent skills
- [github.com/gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills) — security-focused catalog
- [claudskills.com](https://claudskills.com) — 188k+ SKILL.md files crawled from GitHub
- [mastering-claude.com/skills](https://mastering-claude.com/skills/) — 297 curated skills directory
