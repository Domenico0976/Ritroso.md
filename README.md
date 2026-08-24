# Ritroso.md

> Contextual reasoning skill for LLMs — deterministic generation of complete `.md` file sets with multi-layer self-verification, cross-platform Skill Discovery, and autonomous remote-fetch injection.

## What It Is

**Ritroso.md** is a skill framework for LLM models that forces the model to:

1. Classify the project domain and create a dedicated output folder
2. Run a **Skill Discovery & Injection Engine** — finds installed skills, fetches missing ones via raw URL, injects their rules into the relevant files
3. Generate a **complete set of 13 `.md` files** as a structured project map
4. Execute an **Inference Loop** — each file interrogates the others before writing
5. Submit every file to a **Panel of 4 Agents** (Architect, Designer, Pragmatist, Critic) — any BLOCK forces regeneration
6. Run a **negative self-verification** — the model attacks its own output and must defend it with cross-file consistency

The name "Ritroso" means the movement: **the model goes forward, then turns back to verify**.

---

## What's New in v1.4

- **Method C — Remote Fetch**: if no local skill is found, the agent fetches skill rules directly from raw GitHub URLs — no install required, no user prompt needed
- **Active Agent Install Protocol**: when shell access is available, the agent installs missing skills autonomously (Method B via curl), re-runs discovery, and falls back to remote-fetch if install fails
- **CRITIC agent** now blocks if a remote-fetched skill covers a hard limit not present in `08_LIMITS`
- **PHASE 3 and PHASE 4** explicitly documented in PLAN.md
- **Discovery Log** updated with remote-fetch and agent-install sections

---

## Quick Flow (v1.4)

```
[USER PROMPT]
      ↓
[PHASE 0] Classify domain → count ambiguities → GATE 0
      ↓
[SKILL DISCOVERY ENGINE]
  Method 1: Context scan (zero cost)
  Method 2: Direct path scan (all OS + alternative agents)
  Method 3: Grep fallback (find / Get-ChildItem)
  Method C: Remote fetch from catalog raw URLs  ← NEW v1.4
  Method 4: Inference from config files
      ↓
[INJECT] skill rules into target files with [SKILL:name] tags
      ↓
[PHASE 1] Inference Loop — 12 inter-file questions
      ↓
[PHASE 2] Panel of Agents — ARCHITECT · DESIGNER · PRAGMATIST · CRITIC
      ↓
[PHASE 3] Generate 13 .md files in fixed order
      ↓
[PHASE 4] Close Gate — 8 conditions must pass
      ↓
[00_INDEX.md] with Skill Discovery Log + [RITROSO-VERIFIED]
```

---

## How to Install

### Method A — Full install (recommended)

Clone the repository and copy the `skills/` folder to the correct location for your agent:

```bash
# macOS / Linux
git clone https://github.com/Domenico0976/Ritroso.md.git
cp -r Ritroso.md/skills/. ~/.claude/skills/
```

```powershell
# Windows PowerShell
git clone https://github.com/Domenico0976/Ritroso.md.git
Copy-Item -Recurse Ritroso.md\skills\* "$env:APPDATA\Claude\skills\"
```

### Method B — Single skill via curl

Download only the `RITROSO.md` file into your skill folder:

```bash
# macOS / Linux
curl -L https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/RITROSO.md \
  -o ~/.claude/skills/ritroso/SKILL.md
```

```powershell
# Windows PowerShell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/RITROSO.md" `
  -OutFile "$env:APPDATA\Claude\skills\ritroso\SKILL.md"
```

### Method C — Inject via URL (no install)

No installation needed. If your agent supports HTTP fetch, it will load the rules directly from the raw URL on every generation:

```
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/RITROSO.md
```

Paste this URL into the context, the system prompt, or tell the agent:

> "Load rules from: https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/RITROSO.md"

Note: rules injected via URL are valid **for the current generation only** and are tagged `[SKILL:ritroso:remote-fetch]` in the output.

---

## How to Use

1. Install the skill using one of the methods above (or use the raw URL)
2. Provide a project prompt to your agent
3. The system automatically classifies the domain
4. The Skill Discovery Engine finds, fetches, or installs relevant skills
5. If critical context is missing, you will be asked **one question at a time**
6. The 13 generated files are saved in `new-ideas/{domain}/{project}/`
7. `00_INDEX.md` includes the **Discovery Log** listing every method used

---

## Structure

```
Ritroso.md/
├── skills/
│   ├── RITROSO.md              # Main skill (PLAN.md v1.4)
│   ├── PLAN.md                 # Protocol PLAN — Skill Discovery & full flow
│   └── context-mapper.yaml     # Auxiliary skill — multimodal resource mapping
├── prompts/
│   ├── negative-verification.md
│   ├── project-template.md
│   ├── context-gap-questions.md
│   ├── inference-loop.md
│   └── file-set-templates/     # Templates for all 13 output files
├── new-ideas/                  # Generated outputs — organized by domain/project
│   └── {domain-slug}/
│       └── {project-slug}/
│           ├── 00_INDEX.md
│           └── ... (13 files total)
└── docs/
    ├── how-it-works.md         # Technical documentation (deep dive)
    └── RITROSO_DEEP_DIVE.md    # Full implementation reference (v1.4)
```

---

## The 13 Files of the Set

| # | File | Answers |
|---|------|---------|
| 00 | `INDEX` | Navigation map — includes Skill Discovery Log |
| 01 | `GOAL` | Why the project exists |
| 02 | `PRODUCT` | What it does in detail |
| 03 | `NEXT_STEPS` | What to do now, ordered P1/P2/P3 |
| 04 | `ELEMENTS` | What the system contains |
| 05 | `COMPONENTS` | How it is built |
| 06 | `PRICE` | How much it costs for the user |
| 07 | `BUDGET` | How much it costs to produce |
| 08 | `LIMITS` | What cannot/must not be done — Hard Limits |
| 09 | `AGENTS` | Who does what (AI + humans) |
| 10 | `ERROR` | What can go wrong — failure scenarios |
| 11 | `INTERPOLATION` | How everything connects — conflicts flagged |
| 12 | `ASKED` | What we don't know yet — assumptions tagged |

---

## Skill Discovery Engine — Summary

The engine runs in cascade across 5 methods:

| Method | Trigger | Action |
|--------|---------|--------|
| 1 — Context | Always | Scans current session for loaded skills |
| 2 — Path scan | If Method 1 = 0 results | Scans all known OS paths + alternative agents |
| 3 — Grep | If Method 2 = 0 or access denied | `find` / `Get-ChildItem` from project root |
| C — Remote fetch | If all local methods = 0 | HTTP GET catalog raw URLs, inject inline |
| 4 — Inference | Last resort | Reads config files for skill name clues |

Every injected rule is tagged: `[SKILL:name]` for local, `[SKILL:name:remote-fetch]` for remote.

---

## Panel of Agents

Every generated file passes through 4 agents before it can be closed:

- 🏛 **ARCHITECT** — architectural consistency and scalability
- 🎨 **DESIGNER** — clarity, usability, user flow
- ⚙️ **PRAGMATIST** — buildability within budget and team
- 🔒 **CRITIC** — failure scenarios, security gaps, unresolved blocks

A `BLOCK` from any agent forces file regeneration. Files with open BLOCKs cannot be closed.

---

## Philosophy

> *"The model must be wrong before it can be right."*

Negative verification is not destructive — it is the mechanism that transforms a mediocre output into a coherent one. The inference loop adds a second layer: **coherence is built before generation, not just verified after**.

In v1.4, the skill layer adds a third: **context enriches itself autonomously** — the agent finds the rules it needs without waiting for the user to install them.

---

## Documentation

- [PLAN.md](./skills/PLAN.md) — Full protocol specification (v1.4)
- [docs/RITROSO_DEEP_DIVE.md](./docs/RITROSO_DEEP_DIVE.md) — Deep-dive technical reference for every implemented component
- [docs/how-it-works.md](./docs/how-it-works.md) — v3 flow overview
