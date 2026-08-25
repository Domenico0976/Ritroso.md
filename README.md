# Ritroso.md

> Contextual reasoning skill for LLMs — deterministic generation of complete `.md` file sets with multi-layer self-verification, cross-platform Skill Discovery, and autonomous remote-fetch injection.

---

## ⚡ Instant activation (no install)

> **To use Ritroso right now**, copy the block below and paste it as your **first message** in the chat with your model (Claude, GPT-4o, Gemini, Perplexity, etc.).
> The model will treat it as an active skill immediately.

```
Load this skill and treat it as active for the entire conversation:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL_COMPACT.md

If you cannot fetch the URL, use this self-contained version:
---
name: "Ritroso"
version: "5"
description: "Ritroso v5: generates a complete 13-file .md set with inference loop, Panel of 4 Agents (ARCHITECT/DESIGNER/PRAGMATIST/CRITIC), Skill Discovery Engine, gate system, and retroactive self-verification."
---
Operative rules:
- PHASE 0: read the prompt literally, classify domain, count structural ambiguities. If ≥2 → ask ONE blocking question, wait.
- PHASE 1 (inference loop, internal): tag every assumption [INFERRED-FROM-TEXT] or [ASSUMED-NO-BASIS]. Check goal compatibility.
- PHASE 2 (Panel of Agents, on every file): ARCHITECT blocks structural contradictions. DESIGNER blocks steps with no concrete output or missing user flow. PRAGMATIST blocks if scope>budget+50% or no contingency in budget. CRITIC blocks if <3 failure scenarios or [ASSUMED-NO-BASIS] with no entry in 10_ERROR. BLOCK = regenerate the file.
- PHASE 3: generate 13 files in order (00_INDEX → 01_GOAL → 02_PRODUCT → 03_NEXT_STEPS → 04_ELEMENTS → 05_COMPONENTS → 06_PRICE → 07_BUDGET → 08_LIMITS → 09_AGENTS → 10_ERROR → 11_INTERPOLATION → 12_ASKED) in folder new-ideas/{domain}/{project}/. Every file with project-specific YAML frontmatter.
- PHASE 4: Close Gate — all 13 files present, no open BLOCKs, 08_LIMITS has a hard limit per injected skill, 12_ASKED has no [ASSUMED-NO-BASIS] without entry in 10_ERROR.
Full protocol (Skill Discovery Engine, injection rules, agent specs): https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
```

---

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

- **Method C — Remote Fetch**: if no local skill is found, the agent fetches skill rules directly from raw GitHub URLs — no install required
- **Active Agent Install Protocol**: when shell access is available, the agent installs missing skills autonomously
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
  Method 3: Grep fallback
  Method C: Remote fetch from catalog raw URLs
  Method 4: Inference from config files
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

## Install Methods

### Method 0 — Copy and paste (no install, works everywhere)

Copy the block in the ⚡ section above and paste it as your first message in the chat.

### Method A — Full install

```bash
# macOS / Linux
git clone https://github.com/Domenico0976/Ritroso.md.git
mkdir -p ~/.claude/skills/Ritroso
cp Ritroso.md/SKILL.md ~/.claude/skills/Ritroso/SKILL.md
cp Ritroso.md/skills/PLAN.md ~/.claude/skills/Ritroso/PLAN.md
```

```powershell
# Windows PowerShell
git clone https://github.com/Domenico0976/Ritroso.md.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\Ritroso"
Copy-Item Ritroso.md\SKILL.md "$env:USERPROFILE\.claude\skills\Ritroso\SKILL.md"
Copy-Item Ritroso.md\skills\PLAN.md "$env:USERPROFILE\.claude\skills\Ritroso\PLAN.md"
```

### Method B — Single curl (no git)

```bash
# macOS / Linux
mkdir -p ~/.claude/skills/Ritroso
curl -o ~/.claude/skills/Ritroso/SKILL.md \
  https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md
curl -o ~/.claude/skills/Ritroso/PLAN.md \
  https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
```

```powershell
# Windows PowerShell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\Ritroso"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\Ritroso\SKILL.md"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\Ritroso\PLAN.md"
```

### Method C — Raw URL in context

If your agent supports HTTP fetch, paste one of these URLs into the context:

```
# Compact version (recommended for paste-in):
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL_COMPACT.md

# Full version:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md

# Full protocol:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
```

### npx (markdownskill.com)

```bash
npx skills add Domenico0976/Ritroso.md
```

---

## How to Use

1. Activate the skill (any method above, or paste the ⚡ block)
2. Provide the project prompt to your model
3. The system automatically classifies the domain
4. The Skill Discovery Engine finds, fetches, or installs relevant skills
5. If critical context is missing, you will be asked **one question at a time**
6. The 13 generated files are saved in `new-ideas/{domain}/{project}/`
7. `00_INDEX.md` includes the **Discovery Log** listing every method used

---

## Structure

```
Ritroso.md/
├── SKILL.md                    # Full entry point (agents with filesystem/fetch access)
├── SKILL_COMPACT.md            # Compact entry point (paste into any chat)
├── skills/
│   ├── PLAN.md                 # Full execution protocol (v1.4)
│   └── context-mapper.yaml     # Auxiliary skill
├── prompts/
├── new-ideas/                  # Generated output
└── docs/
    ├── RITROSO_DEEP_DIVE.md    # Full technical reference (v1.4)
    └── how-it-works.md
```

---

## The 13 Files

| # | File | Answers |
|---|------|---------|
| 00 | `00_INDEX.md` | Navigation map + Skill Discovery Log |
| 01 | `01_GOAL.md` | Why the project exists |
| 02 | `02_PRODUCT.md` | What it does — user flows P1/P2 |
| 03 | `03_NEXT_STEPS.md` | Roadmap with owners and sign-off conditions |
| 04 | `04_ELEMENTS.md` | All required elements |
| 05 | `05_COMPONENTS.md` | Full tech stack + injected skill rules |
| 06 | `06_PRICE.md` | Pricing strategy and tiers |
| 07 | `07_BUDGET.md` | Build cost + contingency |
| 08 | `08_LIMITS.md` | Hard limits and forbidden patterns |
| 09 | `09_AGENTS.md` | Decision owners and fallbacks |
| 10 | `10_ERROR.md` | ≥3 concrete failure scenarios |
| 11 | `11_INTERPOLATION.md` | Cross-file contradictions |
| 12 | `12_ASKED.md` | Open questions tagged and consequenced |

---

## Panel of Agents

- 🏛 **ARCHITECT** — structural consistency and scalability
- 🎨 **DESIGNER** — clarity, usability, actionable user flow
- ⚙️ **PRAGMATIST** — buildability within budget and team
- 🔒 **CRITIC** — failure scenarios, security gaps, unresolved blocks

BLOCK from any agent → file regeneration. No open BLOCKs allowed at close.

---

## Documentation

- [SKILL_COMPACT.md](./SKILL_COMPACT.md) — compact copy-paste version for instant activation
- [SKILL.md](./SKILL.md) — full entry point (agents with fetch/filesystem)
- [skills/PLAN.md](./skills/PLAN.md) — full protocol v1.4 (Skill Discovery Engine, injection rules, agent specs)
- [docs/RITROSO_DEEP_DIVE.md](./docs/RITROSO_DEEP_DIVE.md) — full technical reference

---

## Philosophy

> *"The model must be wrong before it can be right."*

Negative verification is not destructive — it is the mechanism that transforms a mediocre output into a coherent one. The inference loop adds a second layer: **coherence is built before generation, not just verified after**.
