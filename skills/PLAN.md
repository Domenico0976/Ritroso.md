---
name: "PLAN.md"
version: "6.1"
description: "Mandatory execution protocol for the Ritroso skill. Defines generation phases, project rules, gate conditions, Panel of Agents, and the Skill Discovery & Injection Engine — which scans all known skill paths across Windows/Mac/Linux, falls back to grep when direct access is unavailable, detects active LLM Skills (SKILL.md format) from any platform and any user-defined location, and injects their rules as operative instructions into the relevant Ritroso files during generation. v1.4 adds: Method C URL injection (remote-fetch of raw SKILL.md via catalog URLs), auto-fetch behavior (agent fetches and treats remote skill as ACTIVE inline without install), and active agent install protocol (agent performs install steps autonomously when context permits). v6.1 adds: post-generation quality check (encoding validation, goal-pricing consistency, budget alignment, role deduplication) with expanded Panel of Agents BLOCK conditions and 11-condition Close Gate."
applies_to: "All Ritroso file-set generations"
---

# PLAN.md — Ritroso Execution Protocol

> This file is not optional. Every Ritroso generation MUST follow this protocol in order.
> Skipping a phase or a gate is a protocol violation. When in doubt, stop and ask.

---

## PHASE 0 — PROMPT INTAKE, CLASSIFICATION & SKILL DISCOVERY

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

### Step 0.4 — SKILL DISCOVERY & INJECTION ENGINE

> This step is NEVER blocking. It enriches the generation — it does not gate it.
> Skills here are LLM Skills: SKILL.md files installed on the user's machine or passed in context.
> They are NOT external tools, SaaS products, or browser extensions.

---

#### 0.4.0 — SKILL DISCOVERY (cross-platform, multi-path, grep fallback, remote-fetch)

> The agent MUST attempt discovery before any generation begins.
> Discovery is a best-effort read of the filesystem. If access is denied → use grep fallback.
> If grep also fails → attempt remote-fetch (Method C) before falling back to Method 4.
> Never assume skills are absent without attempting all available methods first.

##### Priority order of discovery
Discovery runs in this order. Stop at the first method that returns results — then continue
checking lower-priority methods to catch additional skills in other locations.

**Method 1 — Context scan (zero filesystem access required)**
Scan the current conversation context for:
- Files named `SKILL.md` already present in context (user uploaded or pasted)
- Skill names mentioned explicitly by the user (e.g. "use impeccable", "ponytail is active")
- `load_skill()` calls already executed in this session
- YAML frontmatter blocks with `name:` fields that look like skill declarations

These are **ACTIVE** immediately — no filesystem read needed.

**Method 2 — Direct path scan**
Scan the known skill paths in this order. On each path, list subdirectories and check
for a `SKILL.md` file inside each one.

```
# WINDOWS paths (check all)
%USERPROFILE%\.claude\skills\              → user-level, all projects
%APPDATA%\Claude\skills\                   → alternative AppData location
%LOCALAPPDATA%\Claude\skills\             → LocalAppData variant
<PROJECT_ROOT>\.claude\skills\            → project-level
<PROJECT_ROOT>\skills\                    → bare skills folder at root
<PROJECT_ROOT>\claude\skills\             → lowercase variant

# macOS paths (check all)
~/.claude/skills/                          → user-level, all projects
~/Library/Application Support/Claude/skills/ → macOS app support
<PROJECT_ROOT>/.claude/skills/            → project-level
<PROJECT_ROOT>/skills/                    → bare skills folder at root
<PROJECT_ROOT>/claude/skills/             → lowercase variant

# LINUX paths (check all)
~/.claude/skills/                          → user-level, all projects
~/.config/claude/skills/                  → XDG config standard
~/.local/share/claude/skills/             → XDG data standard
<PROJECT_ROOT>/.claude/skills/            → project-level
<PROJECT_ROOT>/skills/                    → bare skills folder at root
<PROJECT_ROOT>/claude/skills/             → lowercase variant

# CROSS-PLATFORM — Codex CLI, Gemini CLI, OpenCode, Qwen Code
<PROJECT_ROOT>/.codex/skills/
<PROJECT_ROOT>/.gemini/skills/
<PROJECT_ROOT>/.opencode/skills/
<PROJECT_ROOT>/agent-skills/
<PROJECT_ROOT>/llm-skills/
```

For each path found:
1. List all subdirectories
2. Check if `SKILL.md` (case-insensitive: `skill.md`, `Skill.md`) exists inside
3. If yes → read the file, mark skill as ACTIVE
4. Extract: `name`, `description`, `version` from YAML frontmatter if present

**Method 3 — Grep fallback (when direct path access is denied or paths don't exist)**
If Method 2 returns 0 results due to access denial or missing paths, run a recursive
grep from the project root and from the user home directory:

```bash
# From project root
find . -name "SKILL.md" -o -name "skill.md" 2>/dev/null

# From user home (cross-platform equivalent)
# Windows PowerShell:
Get-ChildItem -Path $HOME -Recurse -Filter "SKILL.md" -ErrorAction SilentlyContinue
# macOS/Linux:
find ~ -name "SKILL.md" 2>/dev/null | head -50

# Grep inside found files to confirm they are LLM skills (not random files named SKILL.md):
grep -l "^name:" found_files | xargs grep -l "description:"
```

If grep also fails (no shell access) → proceed to **Method C — Remote Fetch** before Method 4.

**Method C — Remote Fetch (NEW in v1.4)**

> Triggered when: Methods 1, 2, and 3 all return 0 skill results AND the agent has HTTP
> fetch capability. This method does NOT require user action. The agent performs it autonomously.

When no skill is found locally, the agent MUST attempt to fetch the SKILL.md content directly
from the raw URLs in the Skill Category Catalog (§0.4.2). This is a best-effort enrichment step.

**Remote-fetch behavior rules:**

1. **Fetch silently** — do not prompt the user before fetching. Fetch, inject, log.
2. **Treat fetched content as ACTIVE inline** — a successfully fetched SKILL.md is treated
   exactly as if the skill were installed locally. Its rules are extracted and injected into
   the target Ritroso files.
3. **Scope: project-relevant skills only** — do not fetch all 50+ catalog skills.
   Select only the skills whose category matches the project domain classified in §0.2.
   Maximum 5 remote fetches per generation run to avoid excessive latency.
4. **Fetch priority** — within each relevant category, fetch the first skill in the catalog
   table (highest-priority recommendation). If fetch fails (HTTP error, timeout), skip that
   skill silently and continue.
5. **Tag all remotely-fetched rules** with `[SKILL:skill-name:remote-fetch]` to distinguish
   them from locally-installed skills.
6. **Log in Discovery Log** — record every fetch attempt (URL, HTTP status, outcome) under
   `## Skill Discovery Log` in `00_INDEX.md` with `method: remote-fetch`.
7. **No permanent side effects** — remote-fetch does NOT install the skill to disk.
   It enriches the current generation only. The user may install permanently via §0.4.3.

**Remote-fetch execution template:**

```
FOR each project-relevant category (max 5):
  url = first raw URL from §0.4.2 catalog for that category
  response = HTTP GET url
  IF response.status == 200:
    content = response.body
    verify YAML frontmatter contains name: and description:
    extract operative rules
    mark as ACTIVE (source: remote-fetch)
    inject into target files with tag [SKILL:skill-name:remote-fetch]
    log: { skill: name, url: url, status: "fetched", method: "remote-fetch" }
  ELSE:
    log: { skill: name, url: url, status: "fetch-failed", http: status_code, method: "remote-fetch" }
    skip silently — do not block generation
```

**Method 4 — Inference from project structure**
If no filesystem access at all is available AND remote-fetch also returned 0 results:
- Look for `CLAUDE.md`, `.claude/settings.json`, or any config file that lists skills
- Look for `package.json` / `pyproject.toml` / `requirements.txt` mentioning skill names
- Look for import statements or comments referencing known skill names

If a skill name is found via inference → mark as PROBABLE (not ACTIVE). List under
`## Skill Stack — Probable (inferred)` in `00_INDEX.md`.

**Result classification**
| Status | Meaning | Action |
|--------|---------|--------|
| ACTIVE | SKILL.md content is in context or read from disk | Inject rules into target files |
| ACTIVE (remote-fetch) | SKILL.md fetched from raw URL; not installed locally | Inject rules with `[SKILL:name:remote-fetch]` tag; suggest install in INDEX |
| PROBABLE | Name found via inference but content not read | List in INDEX, note as unverified |
| ABSENT | Not found by any method (local or remote) | Recommend in INDEX with install instructions |

##### User-defined skills (always respected)
If the user declares a skill that is not in the known catalog — whether via context, mention,
or a custom SKILL.md file — it is treated as ACTIVE and its rules are injected verbatim.
Custom skills are tagged `[SKILL:custom/skill-name]` to distinguish them from catalog skills.
Never dismiss or downgrade a user-declared skill. Never override a user skill with a catalog
skill that covers the same category.

---

#### 0.4.1 — Skill Detection & Classification

After running §0.4.0:

For each ACTIVE skill (local or remote-fetch):
1. Identify its **category** from the Skill Category Map (§0.4.2)
2. Identify its **injection targets** (which Ritroso files receive its rules)
3. Extract the **specific rules** to inject (not summaries — the actual operative text)
4. Tag every injected rule with `[SKILL:skill-name]` or `[SKILL:skill-name:remote-fetch]`

For each ABSENT but relevant skill:
- Add it to `00_INDEX.md` under `## Skill Stack — Recommended` with install instructions
  (direct GitHub raw URL or `load_skill()` call)
- Do NOT inject placeholder rules — only inject from what is actually loaded

---

#### 0.4.2 — Skill Category Map & Injection Targets

Each category: what skills cover it, which Ritroso files they inject into, curated non-redundant
skill list covering the full spectrum (code → design → marketing → security → devops → AI agents).
Sources: Prat011/awesome-llm-skills, mingrath/awesome-claude-skills, VoltAgent/awesome-agent-skills,
gmh5225/awesome-skills, awesomeclaude.ai, pbakaus/impeccable, mastering-claude.com.

---

##### CATEGORY: `code-quality`
**What it covers**: Code style enforcement, linting, refactoring, naming conventions,
error handling standards, TypeScript strictness, commit hygiene, code review.

**Recommended skills** (install one per subcategory — they overlap):

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `impeccable` | Design audit & code polish: no magic numbers, no dead code, mandatory error handling, consistent naming | [github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable) — 10k+ ★ |
| `simplify` | Reviews recently changed files for reuse, quality, efficiency — spawns 3 parallel review agents | [github.com/anthropics/claude-code-skills/simplify](https://github.com/anthropics/claude-code-skills) |
| `code-reviewer` | Structured code review by severity level | [raw.githubusercontent.com/anthropics/claude-code-skills/main/code-reviewer/SKILL.md](https://raw.githubusercontent.com/anthropics/claude-code-skills/main/code-reviewer/SKILL.md) |
| `systematic-debug` | Methodical debugging: hypothesis → test → verify loop | [raw.githubusercontent.com/anthropics/claude-code-skills/main/debug/SKILL.md](https://raw.githubusercontent.com/anthropics/claude-code-skills/main/debug/SKILL.md) |
| `changelog-generator` | Transforms git commits into user-facing changelogs | [github.com/Prat011/awesome-llm-skills/changelog-generator](https://github.com/Prat011/awesome-llm-skills/tree/main/changelog-generator) |
| `llm-sast-scanner` | SAST taint analysis across 34 vulnerability classes (Java/Python/JS/TS/PHP/.NET) | [github.com/gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `05_COMPONENTS.md` | Coding standards, forbidden patterns, required patterns verbatim from skill |
| `08_LIMITS.md` | Forbidden patterns become hard limits tagged `[SKILL:skill-name]` |
| `03_NEXT_STEPS.md` | Code review step added to P1; commit standard named explicitly |
| `10_ERROR.md` | Code-level risks: unhandled promises, missing error boundaries, no input validation |

---

##### CATEGORY: `ui-ux-design`
**What it covers**: Component design, layout systems, design tokens, accessibility,
responsive patterns, visual hierarchy, interaction design, web standards.

**Recommended skills** (pick by depth needed):

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `ux-ui-pro-max` | Full UX/UI system: component hierarchy, accessibility rules, responsive patterns, interaction design | `.claude/skills/ux-ui-pro-max/` — search [awesomeclaude.ai](https://awesomeclaude.ai/awesome-claude-skills) |
| `website-building` | Production-grade web design: design tokens, typography, motion, CSS/Tailwind, anti-patterns | Perplexity built-in → `load_skill("website-building")` |
| `frontend-design` | Web components, pages, artifacts with modern frontend design principles | [raw.githubusercontent.com/anthropics/claude-code-skills/main/frontend-design/SKILL.md](https://raw.githubusercontent.com/anthropics/claude-code-skills/main/frontend-design/SKILL.md) |
| `artifacts-builder` | Multi-component HTML artifacts with React, Tailwind, shadcn/ui | [github.com/Prat011/awesome-llm-skills/artifacts-builder](https://github.com/Prat011/awesome-llm-skills/tree/main/artifacts-builder) |
| `canvas-design` | Visual art in PNG/PDF: posters, static design pieces using aesthetic principles | [github.com/Prat011/awesome-llm-skills/canvas-design](https://github.com/Prat011/awesome-llm-skills/tree/main/canvas-design) |
| `theme-factory` | Applies font/color themes to slides, docs, reports, HTML artifacts (10 pre-set themes) | [github.com/Prat011/awesome-llm-skills/theme-factory](https://github.com/Prat011/awesome-llm-skills/tree/main/theme-factory) |

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

##### CATEGORY: `frontend-framework`
**What it covers**: React, Next.js, Vue, Svelte, Tailwind — framework-specific patterns,
component generators, routing, state management, CSS migrations.

**Recommended skills** (pick the framework stack in use):

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `react-component-generator` | Scaffolds React components with TypeScript, tests, stories, correct file structure | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `nextjs-patterns` | Next.js App Router: server components, route handlers, middleware, metadata API | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `tailwind-converter` | Converts CSS to Tailwind utility classes preserving responsive behavior | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `vue-composition-api` | Vue 3 components: Composition API, TypeScript, composables, Pinia stores | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `svelte-5-patterns` | Svelte 5: runes, snippets, new event system | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `css-to-styled-components` | Migrates CSS to styled-components with theme support and TypeScript | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `05_COMPONENTS.md` | Framework stack named; component conventions extracted from skill |
| `08_LIMITS.md` | Framework anti-patterns become hard limits |
| `03_NEXT_STEPS.md` | Component scaffold step added to P1 with named framework |

---

##### CATEGORY: `api-backend`
**What it covers**: REST API design, GraphQL schemas, gRPC, authentication flows,
CRUD generation, real-time sync, backend architecture patterns.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `api-design` | REST APIs with OpenAPI specs, error handling, pagination, authentication | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `graphql-schema` | GraphQL schemas with resolvers, data loaders, subscriptions, N+1 prevention | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `grpc-service` | gRPC definitions: Protocol Buffer schemas, server implementations, client stubs | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `crud-generator` | Complete CRUD across frontend/backend/DB with validation and error handling | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `auth-flow` | Auth flows: OAuth 2.0, JWT, session management, MFA, password reset | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |
| `realtime-sync` | Real-time data: WebSockets, SSE, polling with conflict resolution | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `04_ELEMENTS.md` | API layer, auth, and real-time (if needed) as Critical (P1) |
| `05_COMPONENTS.md` | API design pattern and authentication flow named explicitly |
| `08_LIMITS.md` | Auth anti-patterns become hard limits |
| `10_ERROR.md` | Backend risks: no rate limiting, JWT secret in env, N+1 queries |

---

##### CATEGORY: `brand-styleguide`
**What it covers**: Brand identity, logo rules, color palette enforcement, typography standards,
tone of voice, do/don't examples.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `brand-guidelines` | Applies brand colors and typography to artifacts for consistent visual identity | [github.com/Prat011/awesome-llm-skills/brand-guidelines](https://github.com/Prat011/awesome-llm-skills/tree/main/brand-guidelines) |
| `ponytail` | Visual brand system: color tokens, type scale, spacing, brand voice rules | `.claude/skills/ponytail/` — search [awesomeclaude.ai](https://awesomeclaude.ai/awesome-claude-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | Product must be consistent with the named brand styleguide |
| `05_COMPONENTS.md` | Brand tokens (colors, fonts, spacing) as the base design token set |
| `08_LIMITS.md` | Styleguide violations become hard limits tagged `[SKILL:skill-name]` |
| `09_AGENTS.md` | Brand guardian named; who approves any deviation |

---

##### CATEGORY: `copywriting-content`
**What it covers**: Tone of voice, messaging hierarchy, UX writing, CTAs, headline formulas,
content strategy, editorial guidelines.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `content-research-writer` | High-quality content with research, citations, hooks, section feedback | [github.com/Prat011/awesome-llm-skills/content-research-writer](https://github.com/Prat011/awesome-llm-skills/tree/main/content-research-writer) |
| `brainstorming` | Transforms rough ideas into structured designs through alternative exploration | [github.com/Prat011/awesome-llm-skills/brainstorming](https://github.com/Prat011/awesome-llm-skills/tree/main/brainstorming) |
| `internal-comms` | Internal communications: 3P updates, newsletters, FAQs, status reports | [github.com/Prat011/awesome-llm-skills/internal-comms](https://github.com/Prat011/awesome-llm-skills/tree/main/internal-comms) |

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
**What it covers**: Go-to-market, positioning, channel strategy, funnel design, acquisition,
competitive analysis, SEO, lead research, B2B evaluation.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `lead-research-assistant` | Identifies and qualifies leads by analyzing product and target companies | [github.com/Prat011/awesome-llm-skills/lead-research-assistant](https://github.com/Prat011/awesome-llm-skills/tree/main/lead-research-assistant) |
| `competitive-ads-extractor` | Extracts and analyzes competitors' ads: resonant messaging and creatives | [github.com/Prat011/awesome-llm-skills/competitive-ads-extractor](https://github.com/Prat011/awesome-llm-skills/tree/main/competitive-ads-extractor) |
| `domain-name-brainstormer` | Domain ideas + availability check across .com .io .dev .ai | [github.com/Prat011/awesome-llm-skills/domain-name-brainstormer](https://github.com/Prat011/awesome-llm-skills/tree/main/domain-name-brainstormer) |
| `buyer-eval-skill` | B2B vendor evaluation: scores vendors across 7 dimensions, produces scorecards | [github.com/Prat011/awesome-llm-skills/buyer-eval-skill](https://github.com/Prat011/awesome-llm-skills/tree/main/buyer-eval-skill) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `01_GOAL.md` | Market positioning and target segment from skill's framework |
| `02_PRODUCT.md` | Funnel stages mapped to product phases (P1 = acquisition hook, P2 = retention) |
| `03_NEXT_STEPS.md` | Launch checklist: SEO, analytics, channel setup as named P2 items |
| `06_PRICE.md` | Pricing strategy informed by positioning framework |
| `07_BUDGET.md` | Marketing budget added as a separate cost line |
| `10_ERROR.md` | Marketing risks: no analytics day 1, no acquisition channel, no retention hook |
| `12_ASKED.md` | Open questions: ICP? Acquisition channel? Retention hook? |

---

##### CATEGORY: `security-defensive`
**What it covers**: Threat modeling, OWASP, input validation, authentication, authorization,
secrets management, dependency audits, SAST.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `webapp-testing` | Playwright-based testing: frontend functionality, UI behavior, screenshots | [github.com/Prat011/awesome-llm-skills/webapp-testing](https://github.com/Prat011/awesome-llm-skills/tree/main/webapp-testing) |
| `threat-hunting-with-sigma-rules` | Sigma detection rules to hunt for threats and analyze security events | [github.com/Prat011/awesome-llm-skills/threat-hunting-with-sigma-rules](https://github.com/Prat011/awesome-llm-skills/tree/main/threat-hunting-with-sigma-rules) |
| `llm-sast-scanner` | SAST for 34 vulnerability classes across Java/Python/JS/TS/PHP/.NET | [github.com/gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills) |
| `auth-flow` | Auth implementation: OAuth 2.0, JWT, MFA, session management | [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) |

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
**What it covers**: Penetration testing, attack simulation, deepfake detection,
vulnerability research, digital forensics, fuzzing.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `resemble-detect` | Deepfake detection in audio/image/video/text with confidence scores, voice-profile verification | [github.com/Prat011/awesome-llm-skills/resemble-detect](https://github.com/Prat011/awesome-llm-skills/tree/main/resemble-detect) |
| `computer-forensics` | Digital forensics analysis and investigation techniques | [github.com/Prat011/awesome-llm-skills/computer-forensics](https://github.com/Prat011/awesome-llm-skills/tree/main/computer-forensics) |
| `metadata-extraction` | Extracts and analyzes file metadata for forensic purposes | [github.com/Prat011/awesome-llm-skills/metadata-extraction](https://github.com/Prat011/awesome-llm-skills/tree/main/metadata-extraction) |
| `ffuf-web-fuzzing` | Integrates ffuf web fuzzer: fuzzing tasks and vulnerability analysis | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

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
**What it covers**: Test-driven development, Playwright automation, unit tests,
integration tests, E2E tests, test case design, pairwise testing.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `test-driven-development` | TDD workflow: use before writing implementation code for any feature/bugfix | [github.com/Prat011/awesome-llm-skills/test-driven-development](https://github.com/Prat011/awesome-llm-skills/tree/main/test-driven-development) |
| `tdd-guide` | Enforces RED-GREEN-REFACTOR cycle; blocks implementation without failing test first | [raw.githubusercontent.com/anthropics/claude-code-skills/main/tdd/SKILL.md](https://raw.githubusercontent.com/anthropics/claude-code-skills/main/tdd/SKILL.md) |
| `webapp-testing` | Playwright-based testing for local web apps | [github.com/Prat011/awesome-llm-skills/webapp-testing](https://github.com/Prat011/awesome-llm-skills/tree/main/webapp-testing) |
| `pypict-claude-skill` | Pairwise combinatorial test cases (PICT) for requirements and code | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `root-cause-tracing` | Traces errors deep in execution back to the original trigger | [github.com/Prat011/awesome-llm-skills/root-cause-tracing](https://github.com/Prat011/awesome-llm-skills/tree/main/root-cause-tracing) |
| `browser-automation` | Browser automation with Playwright and Puppeteer | [github.com/VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `03_NEXT_STEPS.md` | Testing steps become mandatory P1 items (not P2); test type named per component |
| `05_COMPONENTS.md` | Test coverage requirement per component; test framework named |
| `10_ERROR.md` | QA risks: no regression tests, untested edge cases, no CI gate on tests |

---

##### CATEGORY: `data-analytics`
**What it covers**: Data modeling, event tracking, analytics instrumentation, dashboards,
privacy compliance (GDPR, CCPA), data pipelines.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `csv-data-summarizer` | Analyzes CSV files and generates insights with visualizations automatically | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `d3js-visualization` | D3 charts and interactive data visualizations | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `meeting-insights-analyzer` | Analyzes meeting transcripts for behavioral patterns, speaking ratios, leadership style | [github.com/Prat011/awesome-llm-skills/meeting-insights-analyzer](https://github.com/Prat011/awesome-llm-skills/tree/main/meeting-insights-analyzer) |
| `postgres` | Read-only SQL with defense-in-depth: multi-connection support, safe query patterns | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

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
**What it covers**: Technical writing standards, README templates, proposal formats,
report layouts, EPUB generation, Word/PDF/Markdown output.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `docx` | Create, edit, analyze Word docs with tracked changes, comments, formatting | Anthropic built-in → `load_skill("doc")` |
| `pdf` | Extract text, tables, metadata, merge and annotate PDFs | Anthropic built-in → `load_skill("pdf")` |
| `markdown-to-epub` | Converts markdown and chat summaries into professional EPUB ebooks | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `01_GOAL.md` | Goal stated in one sentence understandable by a non-technical stakeholder |
| `03_NEXT_STEPS.md` | Documentation step per phase: README for P1, full docs for P2 |
| `09_AGENTS.md` | Documentation owner named; format required per deliverable |

---

##### CATEGORY: `financial-spreadsheet`
**What it covers**: Financial modeling, cost tracking, pricing calculators,
budget templates, scenario planning.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `xlsx` | Spreadsheet manipulation: formulas, charts, data transformations | Anthropic built-in → `load_skill("xlsx")` |
| `invoice-organizer` | Organizes invoices and receipts for tax preparation by extracting and renaming consistently | [github.com/Prat011/awesome-llm-skills/invoice-organizer](https://github.com/Prat011/awesome-llm-skills/tree/main/invoice-organizer) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `07_BUDGET.md` | Budget structured as: Fixed / Variable / Time / Contingency (10%) |
| `06_PRICE.md` | Pricing model includes sensitivity analysis: what if cost doubles or revenue halves? |

---

##### CATEGORY: `presentation-slides`
**What it covers**: Presentation structure, pitch deck formulas, slide design,
storytelling frameworks.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `pptx` | Read, generate, adjust slides, layouts, templates | Anthropic built-in → `load_skill("pptx")` |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | Validation check: "Can this product be described in 5 slides? If not, P1 scope is too large." |
| `01_GOAL.md` | Goal expressible as a one-slide executive summary |

---

##### CATEGORY: `data-visualization`
**What it covers**: Chart type selection, data viz principles, color in charts,
accessibility for data displays, interactive visualizations.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `chart` | Plotly and Mermaid charts: pie, line, scatter, bar, theming, PNG output | Perplexity built-in → `load_skill("chart")` |
| `d3js-visualization` | D3 charts and interactive data visualizations | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `algorithmic-art` | Generative art: parametric visuals, creative coding, canvas patterns | [github.com/Prat011/awesome-llm-skills/algorithmic-art](https://github.com/Prat011/awesome-llm-skills/tree/main/algorithmic-art) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `02_PRODUCT.md` | If product includes data display, chart types must be named in the spec |
| `05_COMPONENTS.md` | Chart library and rendering approach added as a named component |
| `08_LIMITS.md` | Data viz anti-patterns become hard limits |

---

##### CATEGORY: `ai-agents-mcp`
**What it covers**: Multi-agent orchestration, MCP server building, agent workflow design,
LLM skill creation, parallel execution, subagent integration.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `mcp-builder` | Guides creation of high-quality MCP servers for integrating external APIs with LLMs | [github.com/Prat011/awesome-llm-skills/mcp-builder](https://github.com/Prat011/awesome-llm-skills/tree/main/mcp-builder) |
| `skill-creator` | Guidance for creating effective Claude Skills with specialized workflows and tool integrations | [github.com/Prat011/awesome-llm-skills/skill-creator](https://github.com/Prat011/awesome-llm-skills/tree/main/skill-creator) |
| `maestro-orchestrate` | Multi-agent orchestration: 22 specialized agents, 4-phase workflows, parallel execution | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `blueprint` | Spec-driven dev: natural language → blueprints → parallel build plans → software | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |
| `skill-seekers` | Converts any documentation website into a Claude skill automatically | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `05_COMPONENTS.md` | Agent architecture named explicitly; MCP server requirements defined |
| `09_AGENTS.md` | Agent roles, handoff protocols, least-privilege security tiers from skill |
| `08_LIMITS.md` | Agent boundaries: what each agent can and cannot do |

---

##### CATEGORY: `git-devops`
**What it covers**: Git workflows, branch management, CI/CD, deployment pipelines,
worktrees, code review, release management.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `git-pushing` | Automates git operations and repository interactions | [github.com/Prat011/awesome-llm-skills/git-pushing](https://github.com/Prat011/awesome-llm-skills/tree/main/git-pushing) |
| `using-git-worktrees` | Creates isolated git worktrees with smart directory selection and safety verification | [github.com/Prat011/awesome-llm-skills/using-git-worktrees](https://github.com/Prat011/awesome-llm-skills/tree/main/using-git-worktrees) |
| `finishing-a-development-branch` | Guides completion of dev work: presents clear options and handles chosen workflow | [github.com/Prat011/awesome-llm-skills/finishing-a-development-branch](https://github.com/Prat011/awesome-llm-skills/tree/main/finishing-a-development-branch) |
| `review-implementing` | Evaluates code implementation plans and aligns with specs | [github.com/Prat011/awesome-llm-skills/review-implementing](https://github.com/Prat011/awesome-llm-skills/tree/main/review-implementing) |
| `deploy-checklist` | Guides production deployment: checklist-driven pre-flight before any release | [raw.githubusercontent.com/anthropics/claude-code-skills/main/deploy/SKILL.md](https://raw.githubusercontent.com/anthropics/claude-code-skills/main/deploy/SKILL.md) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `03_NEXT_STEPS.md` | Git workflow named per phase; branch strategy defined; deployment step added |
| `09_AGENTS.md` | Who owns the main branch; what is a merge blocker; who does code review |
| `08_LIMITS.md` | Forbidden git patterns (force push to main, etc.) become hard limits |

---

##### CATEGORY: `productivity-automation`
**What it covers**: File organization, workflow automation, meeting intelligence,
knowledge capture, Notion integration, task management.

**Recommended skills**:

| Skill | What it does | URL / Install |
|-------|-------------|---------------|
| `file-organizer` | Intelligently organizes files/folders: finds duplicates, suggests better structures | [github.com/Prat011/awesome-llm-skills/file-organizer](https://github.com/Prat011/awesome-llm-skills/tree/main/file-organizer) |
| `notion-spec-to-implementation` | Turns Notion specs into task plans with acceptance criteria and progress tracking | [github.com/Prat011/awesome-llm-skills/notion-spec-to-implementation](https://github.com/Prat011/awesome-llm-skills/tree/main/notion-spec-to-implementation) |
| `notion-knowledge-capture` | Converts chats and decisions into structured Notion pages and database entries | [github.com/Prat011/awesome-llm-skills/notion-knowledge-capture](https://github.com/Prat011/awesome-llm-skills/tree/main/notion-knowledge-capture) |
| `tapestry` | Interlinks and summarizes related documents into knowledge networks | [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) |

**Injection targets**:
| Ritroso File | What gets injected |
|---|---|
| `09_AGENTS.md` | Workflow owner and knowledge management tool named |
| `03_NEXT_STEPS.md` | Automation and knowledge capture steps added per phase |

---

#### 0.4.3 — Skill Install Instructions

> Include these verbatim in `00_INDEX.md` for every ABSENT recommended skill.
> NEW in v1.4: The agent MUST also attempt active installation when the execution context
> permits shell access (Method A or B below). If shell access is confirmed → execute
> installation autonomously, then re-run §0.4.0 to detect the newly installed skill.
> If no shell access → include instructions for the user as static text.

**Active Agent Install Protocol (v1.4)**

When the agent has shell access AND a skill is marked ABSENT but is relevant to the project:
1. Select the install method: prefer Method B (single SKILL.md curl) to avoid downloading
   unnecessary skills. Use Method A only when the user explicitly wants the full catalog.
2. Execute the install command for the user's detected OS (Windows/macOS/Linux).
3. Re-run §0.4.0 after installation to detect the newly installed skill.
4. Log the install in the Discovery Log: `{ skill: name, method: "agent-installed", path: ... }`
5. If install fails (no network, permissions error) → log failure, fall back to Method C
   (remote-fetch) for the current generation, and include manual instructions for the user.

**Method A — Clone the entire awesome-llm-skills repo (recommended for multiple skills):**
```bash
# macOS / Linux
git clone https://github.com/Prat011/awesome-llm-skills /tmp/awesome-llm-skills
cp -r /tmp/awesome-llm-skills/[skill-name] ~/.claude/skills/[skill-name]

# Windows PowerShell
git clone https://github.com/Prat011/awesome-llm-skills $env:TEMP\awesome-llm-skills
Copy-Item -Recurse "$env:TEMP\awesome-llm-skills\[skill-name]" "$env:USERPROFILE\.claude\skills\[skill-name]"
```

**Method B — Download a single SKILL.md directly (no git required):**
```bash
# macOS / Linux (project-level)
mkdir -p .claude/skills/[skill-name]
curl -o .claude/skills/[skill-name]/SKILL.md \
  https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md

# macOS / Linux (user-level, available across all projects)
mkdir -p ~/.claude/skills/[skill-name]
curl -o ~/.claude/skills/[skill-name]/SKILL.md \
  https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md

# Windows PowerShell (user-level)
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\[skill-name]"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\[skill-name]\SKILL.md"
```

**Method C — URL injection / remote-fetch (no install, no user action — NEW in v1.4):**
```
# The agent fetches the raw SKILL.md URL directly and treats it as ACTIVE inline.
# No file is written to disk. Skill is active for this generation only.
# Triggered automatically by §0.4.0 when local discovery returns 0 results.
# Rules injected with tag: [SKILL:skill-name:remote-fetch]
# User can permanently install later via Method A or B.

# Manual equivalent (paste into context):
# Open the raw SKILL.md URL in your browser:
# https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/[skill-name]/SKILL.md
# Copy the content and paste it into your Claude/Gemini/Codex session
# The agent will treat it as ACTIVE immediately
```

**For Perplexity or Anthropic built-in skills** (chart, website-building, doc, pdf, pptx, xlsx):
```
# No install needed — call load_skill("skill-name") in the prompt
# Example: "Load the website-building skill and use it to design the UI"
```

**Skill discovery resources:**
- [github.com/Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills) — primary curated list
- [awesomeclaude.ai/awesome-claude-skills](https://awesomeclaude.ai/awesome-claude-skills) — 204+ skills browsable by category
- [github.com/mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills) — framework-specific skills
- [github.com/VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) — 1000+ agent skills
- [github.com/gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills) — security-focused catalog
- [claudskills.com](https://claudskills.com) — 188k+ SKILL.md files crawled from GitHub
- [mastering-claude.com/skills](https://mastering-claude.com/skills/) — 297 curated skills directory

---

#### 0.4.4 — Injection Execution Rules

These rules govern HOW injection works. They are mandatory.

1. **Inject rules, not pointers.** Never write "refer to skill X for design rules." Extract the
   operative rules and write them inline in the target file under `## Injected Rules — [skill-name]`.

2. **Never block generation** because a skill is absent. Absent skill = recommendation in
   `00_INDEX.md` with install instructions. Present skill = injection into target files.

3. **Tag every injected rule** with `[SKILL:skill-name]` or `[SKILL:skill-name:remote-fetch]`.
   This makes injected rules traceable and auditable. Remote-fetch rules carry the extra tag
   to signal they are not permanently installed.

4. **Injected rules are treated as project rules** during Phase 2 (Panel of Agents). The Panel
   checks injected rules the same way it checks native project rules.

5. **Conflict between two injected skills**: flag as `[SKILL-CONFLICT: skill-a vs skill-b]`
   in `11_INTERPOLATION.md`. Surface to user in `12_ASKED.md`. Do not silently resolve.

6. **Injected rules in `08_LIMITS.md` are always Hard Limits**, regardless of how they are
   classified in the source skill.

7. **Document all active skills in `00_INDEX.md`** under `## Skill Stack — Active`. For each:
   skill name, category, source (local path / remote-fetch URL), files injected into, number of rules injected.

8. **Non-redundancy check**: if two recommended skills cover the same function (e.g.
   `webapp-testing` and `playwright-automation`), recommend only the most capable one.
   Never recommend redundant pairs.

9. **User skills take precedence**: if the user has a custom or non-catalog skill that covers
   the same category as a recommended skill, do NOT also recommend the catalog skill. List
   the user's skill as ACTIVE in the INDEX and skip the recommendation for that category.

10. **Discovery result must be reported**: always report the discovery method used
    (Method 1/2/3/C/4) and the paths scanned in `00_INDEX.md` under `## Skill Discovery Log`.

11. **Remote-fetch is transparent**: every remote-fetch attempt (success or failure) MUST be
    logged in the Discovery Log. Never silently use remote-fetched content without logging it.

12. **Remote-fetch enrichment is generation-scoped**: rules injected via remote-fetch are
    valid only for the current generation run. On the next run, discovery restarts from Method 1.
    To make rules permanent, the user must install the skill via Method A or B.

---

#### 0.4.5 — Skill Stack section in 00_INDEX.md (template)

```markdown
## Skill Discovery Log
- Method used: [1-Context / 2-Direct path / 3-Grep / C-Remote fetch / 4-Inference]
- OS detected: [Windows / macOS / Linux / Unknown]
- Paths scanned:
  - `~/.claude/skills/` → [found N folders / access denied / not found]
  - `<project>/.claude/skills/` → [found N folders / not found]
  - [other paths checked]
- Grep fallback: [ran / not needed / access denied]
- Remote fetch attempts (Method C):
  - `[skill-name]` → [URL] → [200 OK: fetched / 404 / timeout / skipped]
  - [repeat per skill attempted]
- Agent install attempts:
  - `[skill-name]` → [Method B / Method A] → [success: path / failed: reason]
- Custom skills found: [N user-defined SKILL.md files / none]

## Skill Stack

### Active Skills — Detected and injected
| Skill | Source | Category | Injected Into | Rules Injected |
|-------|--------|----------|---------------|----------------|
| [skill-name] | [local path / "context" / "remote-fetch: URL"] | [category] | [file list] | [N rules] |

### Probable Skills — Inferred but not verified
| Skill | Evidence | Category | Action needed |
|-------|----------|----------|---------------|
| [skill-name] | [where inferred from] | [category] | Install or paste SKILL.md |

### Recommended Skills — Not detected, relevant to this project
| Skill | Category | What it would inject | Install |
|-------|----------|---------------------|---------|
| `impeccable` | code-quality | Coding standards → 05_COMPONENTS, 08_LIMITS | [Method B](https://raw.githubusercontent.com/pbakaus/impeccable/main/SKILL.md) |
| `ux-ui-pro-max` | ui-ux-design | Design system, accessibility → 05_COMPONENTS, 08_LIMITS | `.claude/skills/ux-ui-pro-max/` from [awesomeclaude.ai](https://awesomeclaude.ai/awesome-claude-skills) |
| `brand-guidelines` | brand-styleguide | Brand tokens → 05_COMPONENTS, 08_LIMITS | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/brand-guidelines/SKILL.md) |
| `content-research-writer` | copywriting | Tone rules, hooks → 01_GOAL, 02_PRODUCT | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/content-research-writer/SKILL.md) |
| `competitive-ads-extractor` | marketing-growth | GTM, channel → 03_NEXT_STEPS, 07_BUDGET | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/competitive-ads-extractor/SKILL.md) |
| `threat-hunting-with-sigma-rules` | security-defensive | Attack vectors → 08_LIMITS, 10_ERROR | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/threat-hunting-with-sigma-rules/SKILL.md) |
| `test-driven-development` | testing-qa | Test requirements → 03_NEXT_STEPS, 05_COMPONENTS | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/test-driven-development/SKILL.md) |
| `mcp-builder` | ai-agents-mcp | Agent architecture → 05_COMPONENTS, 09_AGENTS | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/mcp-builder/SKILL.md) |
| `git-pushing` | git-devops | Git workflow → 03_NEXT_STEPS, 09_AGENTS | [Method B](https://raw.githubusercontent.com/Prat011/awesome-llm-skills/main/git-pushing/SKILL.md) |
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
- NOTE if the timeline has no buffer for debugging or integration

#### 🔒 CRITIC
**Question**: "What is the most likely way this fails in the first 30 days?"

Rules:
- BLOCK if 10_ERROR has fewer than 3 failure scenarios
- BLOCK if a security skill is injected but no security owner is named in 09_AGENTS
- BLOCK if a `[SKILL:*:remote-fetch]` rule covers a hard limit and that limit is not in 08_LIMITS
- NOTE if 12_ASKED contains ASSUMED-NO-BASIS items not mirrored in 10_ERROR
- NOTE if no rollback plan exists for any P1 deployment step

---

## PHASE 3 — FILE GENERATION

> Write each file in this order. Do not skip. Do not merge files.
> Injected rules from §0.4 are present in the generated content — not in a separate appendix.

### File generation order
1. `00_INDEX.md` — includes Skill Discovery Log + Skill Stack table (see §0.4.5 template)
2. `01_GOAL.md`
3. `02_PRODUCT.md`
4. `03_NEXT_STEPS.md`
5. `04_ELEMENTS.md`
6. `05_COMPONENTS.md`
7. `06_PRICE.md`
8. `07_BUDGET.md`
9. `08_LIMITS.md`
10. `09_AGENTS.md`
11. `10_ERROR.md`
12. `11_INTERPOLATION.md`
13. `12_ASKED.md`

### Per-file gate
After writing each file, run a one-line self-check:
> "Does this file contradict any other file already written?"
If yes → resolve the contradiction before moving to the next file.

---

## PHASE 4 — CLOSE GATE

> The generation is not complete until all close gate conditions pass.

### Close gate conditions
- [ ] All 13 files generated and written
- [ ] `00_INDEX.md` includes Skill Discovery Log with method used and all fetch/install attempts
- [ ] Every ACTIVE skill (local or remote-fetch) has injected rules in at least one target file
- [ ] Every ABSENT recommended skill has install instructions in `00_INDEX.md`
- [ ] `08_LIMITS.md` contains at least one hard limit per injected skill
- [ ] `11_INTERPOLATION.md` flags any `[SKILL-CONFLICT]` or `[GOAL-CONFLICT]` items
- [ ] `12_ASKED.md` has no ASSUMED-NO-BASIS items that are not also in `10_ERROR`
- [ ] Panel of Agents found no unresolved BLOCKs

If any condition fails → return to the relevant phase and resolve before declaring done.
