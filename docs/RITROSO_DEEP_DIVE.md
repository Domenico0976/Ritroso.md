# Ritroso.md — Deep-Dive Technical Reference

This document covers every component of the Ritroso protocol in detail: the file-set structure, the Skill Discovery Engine, the Panel of Agents, and the injection rules. It is the complete reference for understanding every implementation decision.

---

## 1. Protocol Architecture

Ritroso is a **deterministic execution protocol** for LLM agents. It is not a library, a tool, or an API — it is a set of operative instructions the agent follows in a fixed order, with no ability to skip phases or modify the sequence.

The protocol is structured in 5 phases:

```
PHASE 0  → Intake, domain classification, ambiguity count, Skill Discovery
PHASE 1  → Inference Loop: 13 inter-file questions before writing
PHASE 2  → Panel of Agents: 4 agents validate every file
PHASE 3  → File Generation: 13 files in fixed order
PHASE 4  → Close Gate: 8 closing conditions
```

### Non-blocking principle

No failure in skill discovery blocks generation. If all skills are absent → generation proceeds anyway, with install recommendations in `00_INDEX.md`. This guarantees Ritroso always produces output, even in environments with no filesystem access or no network connection.

### Traceability principle

Every rule injected from an external skill carries an explicit tag (`[SKILL:name]` or `[SKILL:name:remote-fetch]`). This makes the output **auditable**: it is always possible to know which rule came from which source.

---

## 2. PHASE 0 — Prompt Intake & Classification

### §0.1 — Literal prompt reading

The prompt is read exactly as provided. The agent does **not improve**, paraphrase, or add technical terminology. The user's raw text is the absolute ground truth.

This rule exists to prevent "intent drift": LLMs tend to assume the user means something more sophisticated than what they wrote, leading to file sets that do not match the actual project.

### §0.2 — Domain classification

The project is assigned to one of six domains:

| Domain | Examples |
|--------|----------|
| `software-product` | App, SaaS, tool, API |
| `creative-technical` | Audiovisual, generative art, interactive installation |
| `content-operations` | Editorial, CMS, workflow, publishing |
| `open-source-framework` | Library, skill, prompting framework, dev tool |
| `service-or-agency` | Consulting deliverable, client project |
| `other` | Documented explicitly |

Domain classification is the direct input for **Method C (remote-fetch)**: it determines which skill categories are fetched automatically when local discovery returns empty.

### §0.3 — Ambiguity count and GATE 0

A **structural ambiguity** is any gap that changes the architecture, pricing, scope, or distribution of the project. If the count reaches 2 or more, GATE 0 blocks generation.

The critical rule: the agent asks **one question only** — the one that unblocks the greatest number of other answers. Not a list. This principle — called the "maximum-leverage question" — reduces friction with the user and keeps the dialogue fluid.

---

## 3. PHASE 0.4 — Skill Discovery & Injection Engine

### Engine architecture

The discovery engine is the most sophisticated component of the protocol. It operates in cascade across 5 methods, each activated only if the previous one produced insufficient results.

```
Method 1 (Context)
    ↓ [0 results]
Method 2 (Direct path scan)
    ↓ [0 results or access denied]
Method 3 (Grep fallback)
    ↓ [0 results or no shell access]
Method C (Remote fetch)        ← NEW in v1.4
    ↓ [0 results or no HTTP]
Method 4 (Inference)
```

### Method 1 — Context Scan

This is the zero-cost method: no filesystem access, no HTTP calls. The agent scans the current conversation context looking for:

- `SKILL.md` files already loaded by the user
- Skill names mentioned explicitly ("use impeccable", "ponytail is active")
- `load_skill()` calls already executed in the session
- YAML frontmatter blocks with a `name:` field that look like skill declarations

Any skill found via Method 1 is immediately `ACTIVE` — its rules are injected without further verification.

### Method 2 — Direct Path Scan

Scans **all known paths** for every operating system, including paths for alternative agents (Codex CLI, Gemini CLI, OpenCode, Qwen Code). The engine **does not stop at the first path found**: it continues checking all paths even after finding skills, to detect skills in multiple locations.

For each directory found:
1. List all subdirectories
2. Search for `SKILL.md` (case-insensitive) inside each one
3. If found → read the file, extract `name`, `description`, `version` from the YAML frontmatter
4. Mark as `ACTIVE`

**Windows paths scanned:**
```
%USERPROFILE%\.claude\skills\    %APPDATA%\Claude\skills\
%LOCALAPPDATA%\Claude\skills\   %USERPROFILE%\skills\
%USERPROFILE%\.codex\skills\    %USERPROFILE%\.gemini\skills\
%USERPROFILE%\agent-skills\     %USERPROFILE%\llm-skills\
```

**macOS paths scanned:**
```
~/.claude/skills/
~/Library/Application Support/Claude/skills/
~/.codex/skills/   ~/.gemini/skills/   ~/.opencode/skills/
~/agent-skills/    ~/llm-skills/
```

**Linux paths scanned (XDG standard):**
```
~/.config/claude/skills/   ~/.local/share/claude/skills/
~/.claude/skills/          ~/skills/   ~/claude/skills/
~/.codex/skills/           ~/.gemini/skills/
```

### Method 3 — Grep Fallback

Activated when path scan fails (access denied or nonexistent paths). The agent runs `find` (macOS/Linux) or `Get-ChildItem` (Windows PowerShell) from the project root and the home directory. After finding files, it filters them with grep to verify they contain `name:` and `description:` in the frontmatter — avoiding treating any file named `SKILL.md` by coincidence as a skill.

```bash
# macOS / Linux
find ~ -name "SKILL.md" -not -path "*/node_modules/*" 2>/dev/null
find . -name "SKILL.md" -not -path "*/node_modules/*" 2>/dev/null
```

```powershell
# Windows
Get-ChildItem -Path $HOME -Recurse -Filter "SKILL.md" -ErrorAction SilentlyContinue
```

### Method C — Remote Fetch (v1.4)

This is the most innovative method in v1.4. When all local methods fail, the agent **does not wait for the user to install skills** — it fetches them autonomously.

**Detailed behavior:**

1. **Silent**: no prompt to the user before the fetch
2. **Scoped**: fetches only categories relevant to the domain classified in §0.2 (max 5 fetches per run)
3. **Prioritized**: for each category, the first skill in the §0.4.2 catalog table is fetched
4. **Non-permanent**: no file is written to disk. Rules are injected only for the current generation
5. **Logged**: every attempt (success or failure) is recorded in the Discovery Log in `00_INDEX.md`
6. **Resilient**: if a fetch fails (HTTP error, timeout) → silent skip, generation does not block

**Execution template:**

```
FOR each project-relevant category (max 5):
  url = first raw URL from catalog §0.4.2 for that category
  response = HTTP GET url
  IF response.status == 200:
    content = response.body
    verify YAML frontmatter (name: + description:)
    extract operative rules
    mark as ACTIVE (source: remote-fetch)
    inject with tag [SKILL:name:remote-fetch]
    log: { skill, url, status: "fetched", method: "remote-fetch" }
  ELSE:
    log: { skill, url, status: "fetch-failed", http: status_code }
    silent skip
```

**Key difference from v1.3**: in v1.3 an ABSENT skill only produced recommendations in the INDEX. In v1.4, if the raw URL is known in the catalog, the agent can **bypass installation entirely** and enrich the generation anyway.

### Method 4 — Inference

Absolute last resort. The agent looks for skill traces in config files (`CLAUDE.md`, `package.json`, `pyproject.toml`) or code comments. Skills found this way are classified as `PROBABLE` — not `ACTIVE` — because the content was not read, only the name was inferred.

### Result classification

| Status | Meaning | Action |
|--------|---------|--------|
| `ACTIVE` | SKILL.md read from disk or context | Inject rules into target files |
| `ACTIVE (remote-fetch)` | SKILL.md fetched from raw URL, not installed | Inject with `:remote-fetch` tag; suggest install in INDEX |
| `PROBABLE` | Name found by inference, content not read | List in INDEX, flag as unverified |
| `ABSENT` | Not found by any method (local or remote) | Recommend in INDEX with install instructions |

---

## 4. Skill Catalog — 13 Categories

The §0.4.2 catalog covers the full spectrum from development to security to marketing. Each category specifies: which skills cover it, which Ritroso files receive injection, and what gets injected.

| Category | Main injection targets |
|----------|----------------------|
| `code-quality` | `05_COMPONENTS`, `08_LIMITS`, `10_ERROR` |
| `ui-ux-design` | `02_PRODUCT`, `04_ELEMENTS`, `05_COMPONENTS`, `08_LIMITS` |
| `frontend-framework` | `05_COMPONENTS`, `08_LIMITS`, `03_NEXT_STEPS` |
| `api-backend` | `04_ELEMENTS`, `05_COMPONENTS`, `08_LIMITS`, `10_ERROR` |
| `brand-styleguide` | `02_PRODUCT`, `05_COMPONENTS`, `08_LIMITS`, `09_AGENTS` |
| `copywriting-content` | `01_GOAL`, `02_PRODUCT`, `06_PRICE`, `08_LIMITS` |
| `marketing-growth` | `01_GOAL`, `03_NEXT_STEPS`, `06_PRICE`, `07_BUDGET`, `10_ERROR` |
| `security-defensive` | `04_ELEMENTS`, `05_COMPONENTS`, `08_LIMITS`, `10_ERROR`, `09_AGENTS` |
| `security-offensive` | `10_ERROR`, `08_LIMITS`, `11_INTERPOLATION`, `03_NEXT_STEPS` |
| `testing-qa` | `03_NEXT_STEPS`, `05_COMPONENTS`, `10_ERROR` |
| `data-analytics` | `04_ELEMENTS`, `05_COMPONENTS`, `08_LIMITS`, `10_ERROR` |
| `document-writing` | `01_GOAL`, `03_NEXT_STEPS`, `09_AGENTS` |
| `financial-spreadsheet` | `07_BUDGET`, `06_PRICE` |
| `presentation-slides` | `02_PRODUCT`, `01_GOAL` |
| `data-visualization` | `02_PRODUCT`, `05_COMPONENTS`, `08_LIMITS` |
| `ai-agents-mcp` | `05_COMPONENTS`, `09_AGENTS`, `08_LIMITS` |
| `git-devops` | `03_NEXT_STEPS`, `09_AGENTS`, `08_LIMITS` |
| `productivity-automation` | `09_AGENTS`, `03_NEXT_STEPS` |

**Catalog sources:**
- Perplexity built-in: `chart`, `website-building`
- Anthropic built-in: `doc`, `pdf`, `pptx`, `xlsx`
- External: [Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills), [mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills), [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills), [gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills)

### User skill priority vs catalog

If the user has a custom skill covering the same category as a catalog skill → the user's skill takes **absolute priority**. The catalog skill is not even recommended for that category. Custom skills are tagged `[SKILL:custom/skill-name]`.

---

## 5. Injection Rules

### Rule 1 — Inject rules, not pointers
Never write "see skill X for design rules". Operative rules are extracted and written **inline** in the target file under `## Injected Rules — [skill-name]`. The file must be self-contained.

### Rule 2 — Never block on absent skill
An absent skill never blocks generation. It only produces an entry in `00_INDEX.md` under `## Skill Stack — Recommended` with install instructions.

### Rule 3 — Mandatory tagging
Every injected rule carries the tag `[SKILL:name]` for local skills, `[SKILL:name:remote-fetch]` for URL-fetched skills. This guarantees full traceability in the output.

### Rule 4 — Automatic Hard Limits
Any rule injected into `08_LIMITS.md` automatically becomes a **Hard Limit** — regardless of how it was classified in the source skill. No exceptions.

### Rule 5 — Explicit conflicts
If two injected skills have conflicting rules → the conflict is flagged as `[SKILL-CONFLICT: skill-a vs skill-b]` in `11_INTERPOLATION.md` and surfaced to the user in `12_ASKED.md`. Never resolved silently.

### Rules 11 and 12 — Remote-fetch transparency and scoping (v1.4)
- **Rule 11**: Every remote-fetch attempt (success or failure) must be logged in the Discovery Log. Content fetched remotely must never be used without logging it.
- **Rule 12**: Rules injected via remote-fetch are valid **for the current generation only**. To make them permanent, the user must install the skill via Method A or B.

### Active Agent Install Protocol (v1.4)

When the agent has shell access AND a skill is ABSENT but relevant to the project:

1. Selects Method B (single SKILL.md curl) as default
2. Executes the install command for the detected OS
3. **Re-runs §0.4.0** after install to detect the newly installed skill
4. Logs: `{ skill, method: "agent-installed", path }`
5. If install fails → fallback to Method C for the current generation + manual instructions in the INDEX

---

## 6. PHASE 1 — Inference Loop

Before writing any file, the agent internally runs 12 inference questions — one per file in the set (excluding `00_INDEX.md`). These questions detect contradictions, undocumented assumptions, and goal conflicts before they become problems in the output.

### Assumption classification

Every assumption in `12_ASKED.md` must carry one of two tags:

- `[INFERRED-FROM-TEXT]` — supported by something in the original prompt
- `[ASSUMED-NO-BASIS]` — fills a gap with no textual support

`ASSUMED-NO-BASIS` assumptions are automatically flagged in `10_ERROR.md` as high-uncertainty risks.

### Goal compatibility check

Explicit step: *"Are the goals in `01_GOAL` mutually compatible?"* — if a conflict is found → flagged in `11_INTERPOLATION.md` as `[GOAL-CONFLICT]` with an explicit description.

---

## 7. PHASE 2 — Panel of Agents

Every generated file passes through 4 agents before it can be closed. If an agent raises a `BLOCK`, the file is **regenerated** — not annotated. A file with an open BLOCK cannot be closed.

### 🏛 ARCHITECT

**Guiding question**: *"Does this decision hold if the project doubles in scope, changes platform, or changes target user?"*

Blocks if:
- A component contradicts `08_LIMITS` (including injected `[SKILL:*]` rules)
- P1 next steps include infrastructure that requires P2
- An injected security skill requires a P1 component absent from `04_ELEMENTS`

### 🎨 DESIGNER

**Guiding question**: *"Can someone who did not write this prompt read this file and know exactly what to do next?"*

Blocks if:
- `03_NEXT_STEPS` contains a step with no concrete output
- `02_PRODUCT` has no user flow (minimum 3 steps)
- `09_AGENTS` lists a role with no concrete responsibility

### ⚙️ PRAGMATIST

**Guiding question**: *"Is it actually buildable with the time, budget, and people declared in `07_BUDGET` and `09_AGENTS`?"*

Blocks if:
- P1 scope exceeds budget by >50%
- A component requires expertise not present in `09_AGENTS`
- `06_PRICE` and `07_BUDGET` are inconsistent
- Any P1 step depends on an unresolved OPEN

### 🔒 CRITIC (enhanced in v1.4)

**Guiding question**: *"What is the most likely way this fails in the first 30 days?"*

Blocks if:
- `10_ERROR` has fewer than 3 failure scenarios
- A security skill is injected but no security owner is named in `09_AGENTS`
- **[NEW v1.4]** A `[SKILL:*:remote-fetch]` rule covers a hard limit not present in `08_LIMITS`

---

## 8. PHASE 3 — File Generation

Files are always generated in this fixed order:

```
1.  00_INDEX.md          ← Includes Skill Discovery Log
2.  01_GOAL.md
3.  02_PRODUCT.md
4.  03_NEXT_STEPS.md
5.  04_ELEMENTS.md
6.  05_COMPONENTS.md     ← Includes injected skill rules
7.  06_PRICE.md
8.  07_BUDGET.md
9.  08_LIMITS.md         ← Every injected rule becomes a Hard Limit
10. 09_AGENTS.md
11. 10_ERROR.md
12. 11_INTERPOLATION.md  ← Flags SKILL-CONFLICT and GOAL-CONFLICT
13. 12_ASKED.md          ← All assumptions tagged
```

The order is not arbitrary: each file builds on the previous ones. After each file, the agent runs a self-check: *"Does this file contradict any file already written?"*. If yes → resolve before proceeding.

---

## 9. PHASE 4 — Close Gate

Generation is not complete until all conditions pass:

- [ ] All 13 files generated
- [ ] `00_INDEX.md` includes the Discovery Log with method used and all fetch/install attempts
- [ ] Every ACTIVE skill (local or remote-fetch) has rules injected into at least one target file
- [ ] Every ABSENT recommended skill has install instructions in `00_INDEX.md`
- [ ] `08_LIMITS.md` contains at least one hard limit per injected skill
- [ ] `11_INTERPOLATION.md` flags any `[SKILL-CONFLICT]` or `[GOAL-CONFLICT]`
- [ ] `12_ASKED.md` has no `ASSUMED-NO-BASIS` items not also present in `10_ERROR`
- [ ] The Panel of Agents has no open BLOCKs

---

## 10. The Discovery Log in 00_INDEX.md

```markdown
## Skill Discovery Log
- Method used: [1-Context / 2-Direct path / 3-Grep / C-Remote fetch / 4-Inference]
- OS detected: [Windows / macOS / Linux / Unknown]
- Paths scanned:
  - `~/.claude/skills/` → [found N folders / access denied / not found]
  - `<project>/.claude/skills/` → [found N folders / not found]
- Grep fallback: [ran / not needed / access denied]
- Remote fetch attempts (Method C):
  - `[skill-name]` → [URL] → [200 OK: fetched / 404 / timeout / skipped]
- Agent install attempts:
  - `[skill-name]` → [Method B] → [success: path / failed: reason]
- Custom skills found: [N user-defined SKILL.md files / none]
```

---

## 11. Changelog

### v1.4 (August 2026)

1. **Method C — Remote Fetch** in §0.4.0: autonomous discovery via HTTP fetch of catalog raw URLs. Tag `[SKILL:name:remote-fetch]` for traceability.
2. **Active Agent Install Protocol** in §0.4.3: autonomous install via curl, re-run discovery, fallback to Method C.
3. **§0.4.4 rules 11 and 12**: every fetch logged; remote-fetch is generation-scoped.
4. **CRITIC agent**: new BLOCK for `[SKILL:*:remote-fetch]` covering a hard limit not in `08_LIMITS`.
5. **PHASE 3 and PHASE 4** explicitly documented in PLAN.md.
6. **Discovery Log template** updated with remote-fetch and agent-install sections.
7. **Status `ACTIVE (remote-fetch)`** added to the result classification table.

### v1.3

- Full cross-platform discovery (Windows/macOS/Linux + alternative agents)
- Catalog of 13 categories, 50+ skills with direct raw URLs
- 4 prioritized discovery methods
- Injection rules §0.4.4 (rules 1–10)
- Discovery Log template in `00_INDEX.md`
- User skill priority: custom skills always precede catalog skills for the same category
