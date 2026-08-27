# Ritroso.md

> Contextual reasoning skill for LLMs — deterministic generation of complete `.md` file sets with multi-layer self-verification, cross-platform Skill Discovery, and autonomous remote-fetch injection.
> **Mantra**: create the most reliable instructions possible.

---

## ⚡ Instant activation (no install)

> **To use Ritroso right now**, copy the entire content of `SKILL.md` and paste it as your **first message** in the chat with your model (Claude, GPT-4o, Gemini, Perplexity, etc.).
> The model will treat it as an active skill immediately.

Raw URL for fetch:
```
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md
```

---

## What It Is

**Ritroso.md v6.1** is a skill framework for LLM models that forces the model to:

1. Classify the project domain and create a dedicated output folder
2. Run a **Skill Discovery & Injection Engine** — finds installed skills, fetches missing ones via raw URL, injects their rules into the relevant files
3. Generate a **complete set of 13 `.md` files** as a structured project map
4. Execute an **Inference Loop** — each file interrogates the others before writing
5. Submit every file to a **Panel of 4 Agents** (Architect, Designer, Pragmatist, Critic) — any BLOCK forces regeneration
6. Run a **negative self-verification** — the model attacks its own output and must defend it with cross-file consistency
7. Write `00_INDEX.md` **last** as the master navigation and verification surface
8. Run a **post-generation quality check** — encoding validation, goal-pricing consistency, budget alignment, role deduplication

The name "Ritroso" means the movement: **the model goes forward, then turns back to verify**.

---

## What's New in v6.1

- **Post-generation quality check (STEP 8.5)**: encoding scan, goal-pricing consistency, budget alignment, role deduplication
- **Goal-Pricing consistency**: success metrics in 01_GOAL must be compatible with pricing model in 06_PRICE — e.g. no "recurring purchasers" if pricing is one-time-only
- **Budget alignment**: phase budgets in 03_NEXT_STEPS must explain their relationship to total budget in 07_BUDGET
- **Role deduplication**: same person cannot appear as multiple distinct roles in 09_AGENTS — merge into one
- **Encoding validation**: detects replacement characters (U+FFFD), mojibake, and non-UTF-8 artifacts — regenerates affected files
- **ARCHITECT BLOCK expanded**: now checks goal-pricing compatibility
- **DESIGNER BLOCK expanded**: now checks for encoding errors
- **PRAGMATIST BLOCK expanded**: now checks budget alignment
- **CRITIC BLOCK expanded**: now checks for duplicate roles
- **Close Gate expanded**: 7 → 11 conditions
- **Version**: all files report `Ritroso v6.1`

---

## Quick Flow (v6.0)

```
[USER PROMPT]
      ↓
[STEP 1] Classify domain → identify blocking gaps → ask one question at a time
      ↓
[STEP 2] SKILL DISCOVERY ENGINE
  Method 1: Context scan (zero cost)
  Method 2: Direct path scan (all OS + alternative agents)
  Method 3: Grep fallback
  Method C: Remote fetch from catalog raw URLs
  Method 4: Inference from config files
      ↓
[STEP 3] INFERENCE LOOP — 12 inter-file questions (internal, before writing)
      ↓
[STEP 4] Generate files 01→12 in order
  ⚠ 03_NEXT_STEPS mandatory before continuing
      ↓
[STEP 5] PANEL OF AGENTS — ARCHITECT · DESIGNER · PRAGMATIST · CRITIC (every file)
      ↓
[STEP 6] NEGATIVE VERIFICATION — cross-file attack
      ↓
[STEP 7] Write 00_INDEX.md LAST — Skill Discovery Log + Panel table + Verification Status
      ↓
[STEP 8] CLOSE GATE — all 7 conditions must pass
      ↓
[RITROSO-VERIFIED]
```

---

## Install

### Method 0 — Paste inline (no install, works everywhere)

Copy the entire content of `SKILL.md` and paste it as your first message in the chat.

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

## How to Use

1. Activate the skill (paste `SKILL.md` content or install via Method A)
2. Provide the project prompt to your model
3. The model classifies the domain and asks blocking questions if needed
4. The Skill Discovery Engine finds, fetches, or installs relevant skills
5. The inference loop runs internally before any file is written
6. The 13 generated files are saved in `new-ideas/{domain}/{project}/`
7. `00_INDEX.md` is written last with full verification status

---

## Structure

```
Ritroso.md/
├── SKILL.md                    # Single source of truth — full protocol + all templates (v6.0)
├── skills/
│   └── PLAN.md                 # Legacy protocol doc (kept for backward compat)
├── prompts/
│   └── file-set-templates/     # Individual template files (for reference and versioning)
│       ├── 00_INDEX.md
│       ├── 01_GOAL.md
│       ├── ...
│       └── 12_ASKED.md
├── new-ideas/                  # Generated output (ignored by git)
├── TEST/                       # Test outputs (ignored by git)
├── .gitignore
└── README.md
```

---

## The 13 Files

| # | File | Answers |
|---|------|---------|
| 00 | `00_INDEX.md` | Navigation map + Skill Discovery Log + per-file Panel table (**written LAST**) |
| 01 | `01_GOAL.md` | Why the project exists |
| 02 | `02_PRODUCT.md` | What it does — user flows P1/P2 |
| 03 | `03_NEXT_STEPS.md` | Roadmap with owners and sign-off conditions (**mandatory**) |
| 04 | `04_ELEMENTS.md` | All required elements |
| 05 | `05_COMPONENTS.md` | Full tech stack + injected skill rules |
| 06 | `06_PRICE.md` | Pricing strategy and tiers |
| 07 | `07_BUDGET.md` | Build cost + contingency |
| 08 | `08_LIMITS.md` | Hard limits tagged `[SKILL:name]` — every line |
| 09 | `09_AGENTS.md` | Decision owners, handoff protocol, escalation paths |
| 10 | `10_ERROR.md` | ≥3 concrete failure scenarios with full detail |
| 11 | `11_INTERPOLATION.md` | Cross-file contradictions + explicit none-verification |
| 12 | `12_ASKED.md` | Open questions in structured 4-field format |

---

## Panel of Agents

- 🏛 **ARCHITECT** — structural consistency and scalability
- 🎨 **DESIGNER** — clarity, usability, actionable user flow
- ⚙️ **PRAGMATIST** — buildability within budget and team
- 🔒 **CRITIC** — failure scenarios, security gaps, unresolved blocks

BLOCK from any agent → file regeneration. No open BLOCKs allowed at close.  
Every agent's verdict must be recorded **per file** in the `00_INDEX.md` Panel table.

---

## Philosophy

> *"The model must be wrong before it can be right."*

Negative verification is not destructive — it is the mechanism that transforms a mediocre output into a coherent one. The inference loop adds a second layer: **coherence is built before generation, not just verified after**.

> **Mantra**: *create the most reliable instructions possible.*
