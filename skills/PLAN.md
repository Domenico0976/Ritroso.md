---
name: "PLAN.md"
version: "1.2"
description: "Mandatory execution protocol for the Ritroso skill. Defines generation phases, project rules, gate conditions, Panel of Agents, and the Skill Injection Engine — which detects active LLM Skills (SKILL.md format, installed in .claude/skills/) and injects their rules as operative instructions into the relevant Ritroso files during generation."
applies_to: "All Ritroso file-set generations"
---

# PLAN.md — Ritroso Execution Protocol

> This file is not optional. Every Ritroso generation MUST follow this protocol in order.
> Skipping a phase or a gate is a protocol violation. When in doubt, stop and ask.

---

## PHASE 0 — PROMPT INTAKE, CLASSIFICATION & SKILL INJECTION

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

### Step 0.4 — SKILL INJECTION ENGINE

> This step is NEVER blocking. It enriches the generation — it does not gate it.
> Skills here are LLM Skills: SKILL.md files installed in `.claude/skills/` (project)
> or `~/.claude/skills/` (user), loaded automatically by Claude Code and compatible CLIs.
> They are NOT external tools, SaaS products, or browser extensions.

---

#### 0.4.1 — Skill Detection

Scan the context for active LLM Skills. Look for:
- Files named `SKILL.md` present in context or referenced by the user
- Skill names mentioned explicitly (e.g. "use the impeccable skill")
- Skill folders visible in `.claude/skills/` or `~/.claude/skills/`
- `load_skill` calls already executed in this session

**A skill is ACTIVE** if its SKILL.md content is present in context or has been loaded.
**A skill is ABSENT** if it is not in context — regardless of whether the user has heard of it.

For each ACTIVE skill:
1. Identify its **category** from the Skill Category Map (§0.4.2)
2. Identify its **injection targets** (which Ritroso files receive its rules)
3. Extract the **specific rules** to inject (not summaries — the actual operative text)
4. Tag every injected rule with `[SKILL:skill-name]` for traceability

For each ABSENT but relevant skill:
- Add it to `00_INDEX.md` under `## Skill Stack — Recommended` with install instructions
- Do NOT inject placeholder rules — only inject from what is actually loaded

---

#### 0.4.2 — Skill Category Map & Injection Targets

Each category lists: what skills cover it, which Ritroso files they inject into, and the curated skill list (non-redundant, covering the full spectrum from code to design to marketing to security).

---

##### CATEGORY: `code-quality`
**What it covers**: Code style enforcement, linting rules, refactoring patterns, naming conventions, error handling standards, TypeScript strictness, commit hygiene.

**Recommended skills** (install one, not all — they overlap):
| Skill | What it does | Install |
|-------|-------------|---------|
| `impeccable` | Enforces clean code: no magic numbers, no dead code, consistent naming, mandatory error handling | [github.com/Prat011/awesome-llm-skills/impeccable](https://github.com/Prat011/awesome-llm-skills) |
| `move-code-quality-skill` | Move language quality checklist (Move 2024 Edition) | [github.com/Prat011/awesome-llm-skills/move-code-quality-skill](https://github.com/Prat011/awesome-llm-skills) |
| `changelog-generator` | Transforms git commits into user-facing changelogs | [github.com/Prat011/awesome-llm-skills/changelog-generator](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `05_COMPONENTS.md` | Coding standards, forbidden patterns, required patterns verbatim from skill |
| `08_LIMITS.md` | Forbidden patterns become hard limits tagged `[SKILL:skill-name]` |
| `03_NEXT_STEPS.md` | Code review step added to P1; commit standard named explicitly |
| `10_ERROR.md` | Code-level risks: unhandled promises, missing error boundaries, no input validation |

---

##### CATEGORY: `ui-ux-design`
**What it covers**: Component design, layout systems, design tokens, accessibility, responsive patterns, visual hierarchy, interaction design.

**Recommended skills** (pick by depth needed):
| Skill | What it does | Install |
|-------|-------------|---------|
| `ux-ui-pro-max` | Full UX/UI system: component hierarchy, accessibility rules, responsive patterns, interaction design principles | Search `.claude/skills/ux-ui-pro-max/` |
| `website-building` | Production-grade web design: design tokens, typography, motion, CSS/Tailwind, anti-patterns | [Perplexity built-in — load via `load_skill("website-building")`] |
| `canvas-design` | Visual art in PNG/PDF: posters, static design pieces using aesthetic principles | [github.com/Prat011/awesome-llm-skills/canvas-design](https://github.com/Prat011/awesome-llm-skills) |
| `theme-factory` | Applies font/color themes to slides, docs, reports, HTML artifacts (10 pre-set themes) | [github.com/Prat011/awesome-llm-skills/theme-factory](https://github.com/Prat011/awesome-llm-skills) |
| `artifacts-builder` | Multi-component HTML artifacts with React, Tailwind, shadcn/ui | [github.com/Prat011/awesome-llm-skills/artifacts-builder](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | User flow must include visual states: loading, empty, error. Design system named. |
| `04_ELEMENTS.md` | Design system and component library are Critical (P1), not optional |
| `05_COMPONENTS.md` | Design tokens, component architecture, anti-patterns extracted from skill |
| `08_LIMITS.md` | Visual anti-patterns become hard limits tagged `[SKILL:skill-name]` |
| `09_AGENTS.md` | Designer agent: who approves visual direction, who resolves design/dev conflicts |
| `10_ERROR.md` | Design risks: inconsistent components, no dark mode, accessibility failures, untested mobile |

---

##### CATEGORY: `brand-styleguide`
**What it covers**: Brand identity, logo rules, color palette enforcement, typography standards, tone of voice, do/don't examples.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `brand-guidelines` | Applies brand colors and typography to artifacts for consistent visual identity | [github.com/Prat011/awesome-llm-skills/brand-guidelines](https://github.com/Prat011/awesome-llm-skills) |
| `ponytail` | Visual brand system: color tokens, type scale, spacing, brand voice rules | Search `.claude/skills/ponytail/` |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | Product must be consistent with the named brand styleguide |
| `05_COMPONENTS.md` | Brand tokens (colors, fonts, spacing) as the base design token set |
| `08_LIMITS.md` | Styleguide violations become hard limits tagged `[SKILL:skill-name]` |
| `09_AGENTS.md` | Brand guardian named; who approves any deviation |

---

##### CATEGORY: `copywriting-content`
**What it covers**: Tone of voice, messaging hierarchy, UX writing, CTAs, headline formulas, content strategy, editorial guidelines.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `content-research-writer` | Writes high-quality content with research, citations, hooks, section-by-section feedback | [github.com/Prat011/awesome-llm-skills/content-research-writer](https://github.com/Prat011/awesome-llm-skills) |
| `internal-comms` | Internal communications: 3P updates, newsletters, FAQs, status reports | [github.com/Prat011/awesome-llm-skills/internal-comms](https://github.com/Prat011/awesome-llm-skills) |
| `brainstorming` | Transforms rough ideas into structured designs through alternative exploration | [github.com/Prat011/awesome-llm-skills/brainstorming](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `01_GOAL.md` | Goal statements must use benefit-first framing (not feature-first) |
| `02_PRODUCT.md` | Product description must include a one-line value proposition to the skill's formula |
| `06_PRICE.md` | Pricing copy must follow value-emphasis framing (not cost-emphasis) |
| `08_LIMITS.md` | Forbidden copy patterns become hard limits tagged `[SKILL:skill-name]` |
| `10_ERROR.md` | Copy risks: brand inconsistency, off-tone CTAs, placeholder text in production |

---

##### CATEGORY: `marketing-growth`
**What it covers**: Go-to-market, positioning, channel strategy, funnel design, acquisition, competitive analysis, SEO, lead research.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `lead-research-assistant` | Identifies and qualifies leads by analyzing your product and target companies | [github.com/Prat011/awesome-llm-skills/lead-research-assistant](https://github.com/Prat011/awesome-llm-skills) |
| `competitive-ads-extractor` | Extracts and analyzes competitors' ads to understand resonant messaging and creatives | [github.com/Prat011/awesome-llm-skills/competitive-ads-extractor](https://github.com/Prat011/awesome-llm-skills) |
| `domain-name-brainstormer` | Generates domain ideas and checks availability across .com, .io, .dev, .ai | [github.com/Prat011/awesome-llm-skills/domain-name-brainstormer](https://github.com/Prat011/awesome-llm-skills) |
| `buyer-eval-skill` | B2B vendor evaluation: scores vendors across 7 dimensions, produces scorecards | [github.com/Prat011/awesome-llm-skills/buyer-eval-skill](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `01_GOAL.md` | Market positioning and target segment from skill's framework |
| `02_PRODUCT.md` | Funnel stages mapped to product phases (P1 = acquisition hook, P2 = retention) |
| `03_NEXT_STEPS.md` | Launch checklist steps become named P2 items (SEO, analytics, channel setup) |
| `06_PRICE.md` | Pricing strategy informed by positioning framework |
| `07_BUDGET.md` | Marketing budget added as a separate cost line |
| `10_ERROR.md` | Marketing risks: no analytics day 1, no acquisition channel, no retention hook |
| `12_ASKED.md` | Open questions: Who is the ICP? What is the acquisition channel? What is the retention hook? |

---

##### CATEGORY: `security-defensive`
**What it covers**: Threat modeling, OWASP, input validation, authentication, authorization, secrets management, dependency audits.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `webapp-testing` | Tests local web apps via Playwright: frontend functionality, UI behavior, screenshots | [github.com/Prat011/awesome-llm-skills/webapp-testing](https://github.com/Prat011/awesome-llm-skills) |
| `threat-hunting-with-sigma-rules` | Uses Sigma detection rules to hunt for threats and analyze security events | [github.com/Prat011/awesome-llm-skills/threat-hunting-with-sigma-rules](https://github.com/Prat011/awesome-llm-skills) |
| `postgres` (security mode) | Read-only SQL with defense-in-depth: multi-connection support, safe query patterns | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `04_ELEMENTS.md` | Auth, input validation, secrets management are Critical (P1) — not optional |
| `05_COMPONENTS.md` | Security requirements per component: what must be validated, encrypted, logged |
| `08_LIMITS.md` | Security anti-patterns become hard limits tagged `[SKILL:skill-name]`. No exceptions. |
| `10_ERROR.md` | Security risks injected as High probability/High impact by default |
| `03_NEXT_STEPS.md` | Security review added to P1 (not P2); dependency audit and secrets scan added |
| `09_AGENTS.md` | Security owner named; who runs threat model; who approves auth decisions |

---

##### CATEGORY: `security-offensive`
**What it covers**: Penetration testing, attack simulation, deepfake detection, vulnerability research, digital forensics.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `resemble-detect` | Detects deepfakes in audio/image/video/text with confidence scores, voice-profile verification | [github.com/Prat011/awesome-llm-skills/resemble-detect](https://github.com/Prat011/awesome-llm-skills) |
| `computer-forensics` | Digital forensics analysis and investigation techniques | [github.com/Prat011/awesome-llm-skills/computer-forensics](https://github.com/Prat011/awesome-llm-skills) |
| `metadata-extraction` | Extracts and analyzes file metadata for forensic purposes | [github.com/Prat011/awesome-llm-skills/metadata-extraction](https://github.com/Prat011/awesome-llm-skills) |
| `ffuf-web-fuzzing` | Integrates ffuf web fuzzer for Claude to run fuzzing tasks and analyze vulnerabilities | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `10_ERROR.md` | Attack scenarios as concrete failure modes: "Attacker exploits X via Y to achieve Z" |
| `08_LIMITS.md` | Attack surfaces that must be eliminated at P1 tagged `[SKILL:skill-name]` |
| `11_INTERPOLATION.md` | Attack chain analysis: how failure in file A enables attack on component B |
| `03_NEXT_STEPS.md` | Pentest step added to P2; attack surface review added to P1 close gate |

**Injection rule**: Generate at least 3 concrete attack scenarios for this project type.
Format: `[ATTACK-N] Vector: X. Entry point: Y. Impact: Z. Mitigation required in P1: W.`

---

##### CATEGORY: `testing-qa`
**What it covers**: Test-driven development, Playwright automation, unit tests, integration tests, test case design.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `test-driven-development` | TDD workflow: use before writing implementation code for any feature or bugfix | [github.com/Prat011/awesome-llm-skills/test-driven-development](https://github.com/Prat011/awesome-llm-skills) |
| `webapp-testing` | Playwright-based testing for local web apps | [github.com/Prat011/awesome-llm-skills/webapp-testing](https://github.com/Prat011/awesome-llm-skills) |
| `pypict-claude-skill` | Pairwise combinatorial test cases (PICT) for requirements and code | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `root-cause-tracing` | Traces errors deep in execution back to the original trigger | [github.com/Prat011/awesome-llm-skills/root-cause-tracing](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `03_NEXT_STEPS.md` | Testing steps become mandatory P1 items (not P2); test type named per component |
| `05_COMPONENTS.md` | Test coverage requirement per component; test framework named |
| `10_ERROR.md` | QA risks: no regression tests, untested edge cases, no CI gate on tests |

---

##### CATEGORY: `data-analytics`
**What it covers**: Data modeling, event tracking, analytics instrumentation, dashboards, privacy compliance (GDPR, CCPA).

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `csv-data-summarizer` | Analyzes CSV files and generates insights with visualizations automatically | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `d3js-visualization` | Produces D3 charts and interactive data visualizations | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `meeting-insights-analyzer` | Analyzes meeting transcripts for behavioral patterns, speaking ratios, leadership style | [github.com/Prat011/awesome-llm-skills/meeting-insights-analyzer](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `04_ELEMENTS.md` | Analytics instrumentation is Critical (P1) — not an afterthought |
| `05_COMPONENTS.md` | Data schema and event tracking spec added as a required component |
| `08_LIMITS.md` | Privacy requirements (consent, data minimization) become hard limits |
| `10_ERROR.md` | Data risks: no tracking day 1, PII in plain text, no retention policy |
| `03_NEXT_STEPS.md` | Analytics setup added as named P1 step |

---

##### CATEGORY: `document-writing`
**What it covers**: Technical writing standards, README templates, proposal formats, report layouts, EPUB generation.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `doc` (docx) | Create, edit, analyze Word docs with tracked changes, comments, formatting | [Perplexity built-in — load via `load_skill("doc")`] |
| `pdf` | Extract text, tables, metadata, merge and annotate PDFs | [Perplexity built-in — load via `load_skill("pdf")`] |
| `markdown-to-epub` | Converts markdown and chat summaries into professional EPUB ebooks | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `01_GOAL.md` | Goal stated in one sentence understandable by a non-technical stakeholder |
| `03_NEXT_STEPS.md` | Documentation step per phase: README for P1, full docs for P2 |
| `09_AGENTS.md` | Documentation owner named; format required per deliverable |

---

##### CATEGORY: `financial-spreadsheet`
**What it covers**: Financial modeling, cost tracking, pricing calculators, budget templates, scenario planning.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `xlsx` | Spreadsheet manipulation: formulas, charts, data transformations | [Perplexity built-in — load via `load_skill("xlsx")`] |
| `invoice-organizer` | Organizes invoices and receipts for tax preparation by extracting and renaming consistently | [github.com/Prat011/awesome-llm-skills/invoice-organizer](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `07_BUDGET.md` | Budget structured as: Fixed / Variable / Time / Contingency (10%) |
| `06_PRICE.md` | Pricing model includes sensitivity analysis: what if cost doubles or revenue halves? |

---

##### CATEGORY: `presentation-slides`
**What it covers**: Presentation structure, pitch deck formulas, slide design, storytelling frameworks.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `pptx` | Read, generate, adjust slides, layouts, templates | [Perplexity built-in — load via `load_skill("pptx")`] |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | Validation check: "Can this product be described in 5 slides? If not, P1 scope is too large." |
| `01_GOAL.md` | Goal expressible as a one-slide executive summary |

---

##### CATEGORY: `data-visualization`
**What it covers**: Chart type selection, data viz principles, color in charts, accessibility for data displays.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `chart` | Plotly and Mermaid charts: pie, line, scatter, bar, theming, PNG output | [Perplexity built-in — load via `load_skill("chart")`] |
| `d3js-visualization` | D3 charts and interactive data visualizations | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | If product includes data display, chart types must be named in the spec |
| `05_COMPONENTS.md` | Chart library and rendering approach added as a named component |
| `08_LIMITS.md` | Data viz anti-patterns become hard limits |

---

##### CATEGORY: `ai-agents-mcp`
**What it covers**: Multi-agent orchestration, MCP server building, agent workflow design, LLM skill creation.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `mcp-builder` | Guides creation of high-quality MCP servers for integrating external APIs with LLMs | [github.com/Prat011/awesome-llm-skills/mcp-builder](https://github.com/Prat011/awesome-llm-skills) |
| `skill-creator` | Guidance for creating effective Claude Skills with specialized workflows and tool integrations | [github.com/Prat011/awesome-llm-skills/skill-creator](https://github.com/Prat011/awesome-llm-skills) |
| `maestro-orchestrate` | Multi-agent orchestration: 22 specialized agents, 4-phase workflows, parallel execution | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `blueprint` | Spec-driven development: natural language → blueprints → parallel build plans → working software | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `05_COMPONENTS.md` | Agent architecture named explicitly; MCP server requirements defined |
| `09_AGENTS.md` | Agent roles, handoff protocols, least-privilege security tiers from skill |
| `08_LIMITS.md` | Agent boundaries: what each agent can and cannot do |

---

##### CATEGORY: `git-devops`
**What it covers**: Git workflows, branch management, CI/CD, deployment pipelines, worktrees, code review.

**Recommended skills**:
| Skill | What it does | Install |
|-------|-------------|---------|
| `git-pushing` | Automates git operations and repository interactions | [github.com/Prat011/awesome-llm-skills/git-pushing](https://github.com/Prat011/awesome-llm-skills) |
| `using-git-worktrees` | Creates isolated git worktrees with smart directory selection and safety verification | [github.com/Prat011/awesome-llm-skills/using-git-worktrees](https://github.com/Prat011/awesome-llm-skills) |
| `finishing-a-development-branch` | Guides completion of dev work: presents clear options and handles chosen workflow | [github.com/Prat011/awesome-llm-skills/finishing-a-development-branch](https://github.com/Prat011/awesome-llm-skills) |
| `review-implementing` | Evaluates code implementation plans and aligns with specs | [github.com/Prat011/awesome-llm-skills/review-implementing](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `03_NEXT_STEPS.md` | Git workflow named per phase; branch strategy defined; deployment step added |
| `09_AGENTS.md` | Who owns the main branch; what is a merge blocker; who does code review |
| `08_LIMITS.md` | Forbidden git patterns (force push to main, etc.) become hard limits |

---

#### 0.4.3 — Skill Install Instructions

> Include these verbatim in `00_INDEX.md` for every ABSENT recommended skill.

**For Claude Code (project-level):**
```bash
mkdir -p .claude/skills/[skill-name]
# Download SKILL.md from the awesome-llm-skills repo into this folder
# Claude Code auto-discovers skills from .claude/skills/
```

**For Claude Code (user-level, available across all projects):**
```bash
mkdir -p ~/.claude/skills/[skill-name]
# Download SKILL.md into this folder
```

**For Perplexity built-in skills** (chart, website-building, doc, pdf, pptx, xlsx):
```
# No install needed — call load_skill("skill-name") in the prompt
# Example: "Load the website-building skill and use it to design the UI"
```

**Source repository for all community skills:**
[github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills)

---

#### 0.4.4 — Injection Execution Rules

These rules govern HOW injection works. They are mandatory.

1. **Inject rules, not pointers.** Never write "refer to skill X for design rules." Extract the operative rules and write them inline in the target file under `## Injected Rules — [skill-name]`.

2. **Never block generation** because a skill is absent. Absent skill = recommendation in `00_INDEX.md` with install instructions. Present skill = injection into target files.

3. **Tag every injected rule** with `[SKILL:skill-name]`. This makes injected rules traceable and auditable.

4. **Injected rules are treated as project rules** during Phase 2 (Panel of Agents). The Panel checks injected rules the same way it checks native project rules.

5. **Conflict between two injected skills**: flag as `[SKILL-CONFLICT: skill-a vs skill-b]` in `11_INTERPOLATION.md`. Surface to user in `12_ASKED.md`. Do not silently resolve.

6. **Injected rules in `08_LIMITS.md` are always Hard Limits**, regardless of how they are classified in the source skill.

7. **Document all active skills in `00_INDEX.md`** under `## Skill Stack — Active`. For each: skill name, category, files injected into, number of rules injected.

8. **Non-redundancy check**: if two recommended skills cover the same function (e.g. `webapp-testing` and `playwright-automation`), recommend only the most capable one. Never recommend redundant pairs.

---

#### 0.4.5 — Skill Stack section in 00_INDEX.md (template)

```markdown
## Skill Stack

### Active Skills — Detected in context and injected
| Skill | Category | Injected Into | Rules Injected |
|-------|----------|---------------|----------------|
| [skill-name] | [category] | [file list] | [N rules] |

### Recommended Skills — Not detected, relevant to this project
| Skill | Category | What it would inject | Install |
|-------|----------|---------------------|---------|
| `impeccable` | code-quality | Coding standards → 05_COMPONENTS, 08_LIMITS | `.claude/skills/impeccable/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `ux-ui-pro-max` | ui-ux-design | Design system, accessibility → 05_COMPONENTS, 08_LIMITS | `.claude/skills/ux-ui-pro-max/` |
| `brand-guidelines` | brand-styleguide | Brand tokens → 05_COMPONENTS, 08_LIMITS | `.claude/skills/brand-guidelines/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `content-research-writer` | copywriting-content | Tone rules, hooks → 01_GOAL, 02_PRODUCT | `.claude/skills/content-research-writer/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `competitive-ads-extractor` | marketing-growth | GTM, channel strategy → 03_NEXT_STEPS, 07_BUDGET | `.claude/skills/competitive-ads-extractor/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `threat-hunting-with-sigma-rules` | security-defensive | Attack vectors → 08_LIMITS, 10_ERROR | `.claude/skills/threat-hunting-with-sigma-rules/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `test-driven-development` | testing-qa | Test requirements → 03_NEXT_STEPS, 05_COMPONENTS | `.claude/skills/test-driven-development/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `mcp-builder` | ai-agents-mcp | Agent architecture → 05_COMPONENTS, 09_AGENTS | `.claude/skills/mcp-builder/` from [awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
```

---

## PHASE 1 — INFERENCE LOOP (pre-generation)

> This phase happens entirely before writing any file.
> The model interrogates its own understanding across all 13 file slots.
> Injected rules from Phase 0.4 are active from this point forward.

### Step 1.1 — Run the inter-file inference questions
For each file slot, ask internally:

| File | Inference Question |
|------|-------------------|
| 01_GOAL | Why does this project exist? What problem does it solve for a specific person? |
| 02_PRODUCT | What does it concretely do? Can I describe the user flow in 3 steps? |
| 03_NEXT_STEPS | What is the smallest thing that proves the concept works? |
| 04_ELEMENTS | What must exist for any of this to function? What is not yet named? |
| 05_COMPONENTS | How is it built? Is the stack consistent with 07_BUDGET and 08_LIMITS? Are injected rules reflected? |
| 06_PRICE | What does the user pay? Is this consistent with 01_GOAL? |
| 07_BUDGET | What does it cost to build? Is this consistent with 05_COMPONENTS? |
| 08_LIMITS | What cannot be done? Are any limits in conflict with 01_GOAL or 02_PRODUCT? Are injected hard limits present? |
| 09_AGENTS | Who decides when there is a trade-off? Name the decision owner explicitly. |
| 10_ERROR | What is the most likely failure? What is the worst-case failure? Are injected risks present? |
| 11_INTERPOLATION | Do all files point in the same direction? Any [SKILL-CONFLICT] items? |
| 12_ASKED | What do we not know? Mark each as INFERRED or ASSUMED. Skill-specific open questions present? |

### Step 1.2 — Mark all assumptions
Every assumption in 12_ASKED must carry one of two tags:
- `[INFERRED-FROM-TEXT]` — the assumption is supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — the assumption fills a gap with no textual support

Rule: ASSUMED-NO-BASIS items are higher risk. Flag them in 10_ERROR as well.

### Step 1.3 — Check goal compatibility
Before generating any file, explicitly ask:
> "Are the objectives in 01_GOAL mutually compatible?"
> "Does achieving Goal A make Goal B harder or impossible?"

If a conflict is found: flag in 11_INTERPOLATION as `[GOAL-CONFLICT]` with explicit description.

---

## PHASE 2 — PANEL OF AGENTS

> Every file passes through all 4 agents before being closed.
> Injected skill rules are treated as binding project rules by all agents.
> If any agent raises a BLOCK, the file is regenerated — not annotated.

### The 4 Agents

#### 🏛 ARCHITECT
**Question**: "Does this decision hold if the project doubles in scope, changes platform, or changes target user?"

Rules:
- BLOCK if a component contradicts 08_LIMITS (including injected `[SKILL:*]` limits)
- BLOCK if 03_NEXT_STEPS P1 includes anything that requires P2 infrastructure
- BLOCK if an injected security skill requires a P1 component missing from 04_ELEMENTS
- NOTE if the stack has no clear upgrade path for P3 features
- NOTE if a single point of failure exists with no fallback in 10_ERROR

#### 🎨 DESIGNER
**Question**: "Can a person who did not write this prompt read this file and know exactly what to do next?"

Rules:
- BLOCK if 03_NEXT_STEPS contains a step with no concrete output
- BLOCK if 02_PRODUCT has no user flow (minimum 3 steps)
- BLOCK if 09_AGENTS lists a role with no concrete responsibility
- BLOCK if a `[SKILL:ux-ui-pro-max]` or `[SKILL:brand-guidelines]` rule is violated in 05_COMPONENTS
- NOTE if jargon is used without definition
- NOTE if 12_ASKED questions don't state what they block

#### ⚙️ PRAGMATIST
**Question**: "Is this actually buildable with the time, money, and people declared in 07_BUDGET and 09_AGENTS?"

Rules:
- BLOCK if P1 scope exceeds budget by >50%
- BLOCK if a component requires expertise not in 09_AGENTS
- BLOCK if 06_PRICE and 07_BUDGET are inconsistent
- BLOCK if any P1 step depends on an unresolved OPEN
- BLOCK if an injected marketing skill adds costs not reflected in 07_BUDGET
- NOTE if time estimate has no range

#### ⚠️ RISK
**Question**: "What is the single most likely way this project fails in the first 30 days?"

Rules:
- BLOCK if 10_ERROR has no human/process failure mode
- BLOCK if an `[ASSUMED-NO-BASIS]` item has no risk entry
- BLOCK if negative verification has not been run
- BLOCK if 08_LIMITS has a hard limit violated in 03_NEXT_STEPS
- BLOCK if a security skill (`threat-hunting-with-sigma-rules`, `resemble-detect`, etc.) is active and no High-impact risk appears in 10_ERROR
- NOTE if all risks are Medium/Low
- NOTE if no decision owner exists for the highest-impact trade-off

---

## PHASE 3 — NEGATIVE VERIFICATION

> Runs AFTER all 13 files are drafted, BEFORE they are closed.
> The model must argue against its own output.

### Step 3.1 — Goal attack
> "What is the strongest argument that this goal is wrong, incomplete, or self-contradictory?"
Document in 11_INTERPOLATION under `## Negative Verification — Goal Attack`.

### Step 3.2 — Architecture attack
> "What is the most likely reason this architecture fails in production?"
> "What assumption does this architecture make that is not stated in 08_LIMITS?"
Document in 10_ERROR under `## Negative Verification — Architecture Attack`.

### Step 3.3 — Roadmap attack
> "Which step is most likely to be skipped under time pressure?"
> "Which step, if skipped, causes the most downstream damage?"
Add `## Skippability Risk` to 03_NEXT_STEPS.

### Step 3.4 — Assumption attack
For every `[ASSUMED-NO-BASIS]`: "If wrong, which files must be rewritten?"
List affected files explicitly.

### Step 3.5 — Skill injection attack (v1.2)
For every injected rule tagged `[SKILL:*]`, ask:
> "Is this rule actually followed in the file it was injected into?"
> "Does any injected rule contradict a native project rule?"
If a violation is found: regenerate the affected file.
If a contradiction is found: flag as `[SKILL-CONFLICT]` in 11_INTERPOLATION.

### GATE 3
**Condition**: Any new BLOCK-level finding from Phase 3 must be resolved before the file set is closed.
File set is only marked `RITROSO-VERIFIED` after Gate 3 with no open BLOCKs.

---

## PHASE 4 — FILE CLOSURE & INDEX

### Step 4.1 — Write 00_INDEX.md last
After all other 12 files are closed and verified.

### Step 4.2 — Verification status block
```
## Verification Status
- Inference loop: COMPLETE / INCOMPLETE
- Panel of Agents: PASSED / BLOCKS REMAINING (list)
- Negative verification: PASSED / OPEN ATTACKS (list)
- Skill injection attack: PASSED / VIOLATIONS (list)
- Open questions: N (list OPEN IDs)
- Goal conflicts: N (list IDs or NONE)
- Skill conflicts: N (list [SKILL-CONFLICT] IDs or NONE)
- ASSUMED-NO-BASIS items: N (list or NONE)

## Skill Stack
### Active Skills — Detected and injected
| Skill | Category | Injected Into | Rules Injected |
|-------|----------|---------------|----------------|

### Recommended Skills — Not detected
| Skill | Category | What it would inject | Install |
|-------|----------|---------------------|---------|
```

### Step 4.3 — Do not mark RITROSO-VERIFIED if:
- Any BLOCK from Panel of Agents is unresolved
- Any Phase 3 attack finding is unaddressed
- Any injected `[SKILL:*]` security rule is not present in 08_LIMITS or 10_ERROR
- Any `[SKILL-CONFLICT]` is unresolved in 11_INTERPOLATION
- Any P1 step depends on an unresolved OPEN

---

## PROJECT RULES (mandatory for all generations)

### Rules from 01_GOAL
- R-GOAL-1: Every goal must name a specific person or role who benefits.
- R-GOAL-2: Anti-goals are mandatory. At least one.
- R-GOAL-3: Two goals must be checked for mutual compatibility before any file is written.

### Rules from 02_PRODUCT
- R-PROD-1: MVP in one sentence + 3-step user flow.
- R-PROD-2: P1/P2/P3 explicitly separated. No unphased features.
- R-PROD-3: "Future features" without a phase label are banned.

### Rules from 03_NEXT_STEPS
- R-STEPS-1: Every step has a concrete output.
- R-STEPS-2: No P1 step depends on P2/P3 infrastructure.
- R-STEPS-3: Skippability Risk section is mandatory.

### Rules from 04_ELEMENTS
- R-ELEM-1: Every element classified as Critical/Important/Optional.
- R-ELEM-2: Minimum Viable Set explicitly listed.

### Rules from 05_COMPONENTS
- R-COMP-1: Stack cross-checked against 07_BUDGET and 08_LIMITS.
- R-COMP-2: Every component appears in 04_ELEMENTS.
- R-COMP-3: Architecture includes a node pipeline diagram.
- R-COMP-4: Active injected skills listed under `## Injected Rules — [skill-name]` per skill.

### Rules from 06_PRICE
- R-PRICE-1: TBD price requires a decision deadline.
- R-PRICE-2: Price model consistent with 01_GOAL.

### Rules from 07_BUDGET
- R-BUDG-1: All estimates are ranges.
- R-BUDG-2: Variable costs separated from fixed costs.
- R-BUDG-3: Budget reality check question included.

### Rules from 08_LIMITS
- R-LIM-1: Every hard limit states the reason.
- R-LIM-2: At least 3 anti-patterns.
- R-LIM-3: Limit conflicting with a goal = `[GOAL-CONFLICT]` in 11_INTERPOLATION.
- R-LIM-4: All injected `[SKILL:*]` limits are treated as Hard Limits, no exceptions.

### Rules from 09_AGENTS
- R-AGNT-1: Every agent has at least one named decision point.
- R-AGNT-2: Trade-off owner named by rule.
- R-AGNT-3: Handoff points explicit.

### Rules from 10_ERROR
- R-ERR-1: At least one human/process failure mode.
- R-ERR-2: Every `[ASSUMED-NO-BASIS]` has a risk entry.
- R-ERR-3: At least one High risk.
- R-ERR-4: Every risk has Probability and Impact labeled.
- R-ERR-5: If a security skill is active, at least 3 concrete attack scenarios present.

### Rules from 11_INTERPOLATION
- R-INTERP-1: Dependency chain is causal (A → B), not a list.
- R-INTERP-2: Every `[GOAL-CONFLICT]` documented with resolution options.
- R-INTERP-3: Negative verification findings section mandatory.
- R-INTERP-4: Every `[SKILL-CONFLICT]` documented with conflicting rules named explicitly.

### Rules from 12_ASKED
- R-ASKED-1: Every question states which file(s) it blocks.
- R-ASKED-2: Every assumption tagged `[INFERRED-FROM-TEXT]` or `[ASSUMED-NO-BASIS]`.
- R-ASKED-3: No open question without a tagged safest assumption.
- R-ASKED-4: Questions sorted by blocking priority.

### Rules from 00_INDEX
- R-INDEX-1: Skill Stack section mandatory in all generations.
- R-INDEX-2: Active and recommended skills never merged into one list.
- R-INDEX-3: Every recommended skill includes install instructions (path or `load_skill()` call).
- R-INDEX-4: Non-redundant recommendations only — never recommend two skills that do the same thing.

---

## QUICK REFERENCE — GATE SUMMARY

| Gate | Trigger | Action |
|------|---------|--------|
| GATE 0 | Structural ambiguities ≥ 2 | Ask one blocking question. Do not generate. |
| GATE 1 | Goal conflict found | Flag `[GOAL-CONFLICT]`. Do not proceed silently. |
| GATE 2 | Panel of Agents BLOCK | Regenerate the file. Do not annotate. |
| GATE 3 | Negative verification BLOCK | Regenerate affected files. |
| GATE 4 | P1 step needs unresolved OPEN | Remove from P1 or resolve first. |
| GATE 5 | Injected skill rule violated in target file | Regenerate target file with rule applied. |

---

## QUICK REFERENCE — SKILL INJECTION SUMMARY

| Category | Primary Skills | Primary Injection Targets |
|---|---|---|
| `code-quality` | `impeccable`, `changelog-generator` | 05_COMPONENTS, 08_LIMITS, 03_NEXT_STEPS |
| `ui-ux-design` | `ux-ui-pro-max`, `website-building`, `artifacts-builder` | 05_COMPONENTS, 08_LIMITS, 02_PRODUCT |
| `brand-styleguide` | `brand-guidelines`, `ponytail` | 05_COMPONENTS, 08_LIMITS, 09_AGENTS |
| `copywriting-content` | `content-research-writer`, `brainstorming` | 01_GOAL, 02_PRODUCT, 08_LIMITS |
| `marketing-growth` | `competitive-ads-extractor`, `lead-research-assistant` | 03_NEXT_STEPS, 06_PRICE, 07_BUDGET |
| `security-defensive` | `threat-hunting-with-sigma-rules`, `webapp-testing` | 08_LIMITS, 10_ERROR, 04_ELEMENTS |
| `security-offensive` | `resemble-detect`, `computer-forensics`, `ffuf-web-fuzzing` | 10_ERROR, 08_LIMITS, 11_INTERPOLATION |
| `testing-qa` | `test-driven-development`, `pypict-claude-skill` | 03_NEXT_STEPS, 05_COMPONENTS |
| `data-analytics` | `csv-data-summarizer`, `d3js-visualization` | 04_ELEMENTS, 05_COMPONENTS, 10_ERROR |
| `document-writing` | `doc`, `pdf`, `markdown-to-epub` | 03_NEXT_STEPS, 09_AGENTS |
| `financial-spreadsheet` | `xlsx`, `invoice-organizer` | 07_BUDGET, 06_PRICE |
| `presentation-slides` | `pptx` | 02_PRODUCT, 01_GOAL |
| `data-visualization` | `chart`, `d3js-visualization` | 05_COMPONENTS, 08_LIMITS |
| `ai-agents-mcp` | `mcp-builder`, `maestro-orchestrate`, `blueprint` | 05_COMPONENTS, 09_AGENTS |
| `git-devops` | `git-pushing`, `finishing-a-development-branch` | 03_NEXT_STEPS, 09_AGENTS, 08_LIMITS |

---

## VERSION HISTORY
| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-08-24 | Initial release — timeline, project rules, panel of agents |
| 1.1 | 2026-08-24 | Added Phase 0.4 Skill Awareness — domain-to-tool map (external tools, SaaS links) |
| 1.2 | 2026-08-24 | Replaced tool map with real LLM Skills (SKILL.md format) — 15 categories, skills from awesome-llm-skills + Perplexity built-ins, detection logic (active vs absent), injection targets per file, install instructions, GATE 5, Step 3.5, R-INDEX-4, non-redundancy rule |
