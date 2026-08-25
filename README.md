# Ritroso.md

> Contextual reasoning skill for LLMs — deterministic generation of complete `.md` file sets with multi-layer self-verification, cross-platform Skill Discovery, and autonomous remote-fetch injection.

---

## ⚡ Attivazione immediata (nessun install)

> **Per usare Ritroso adesso**, copia il blocco qui sotto e incollalo come **primo messaggio** nella chat con il tuo modello (Claude, GPT-4o, Gemini, Perplexity, ecc.).
> Il modello la tratterà come skill attiva immediatamente.

```
Carica questa skill e trattala come attiva per tutta la conversazione:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL_COMPACT.md

Se non riesci a fare fetch dell'URL, usa questa versione già pronta:
---
name: "Ritroso"
version: "5"
description: "Ritroso v5: genera set di 13 file .md con inference loop, Panel of 4 Agents (ARCHITECT/DESIGNER/PRAGMATIST/CRITIC), Skill Discovery Engine, gate system e verifica retroattiva."
---
Regole operative:
- PHASE 0: leggi il prompt esattamente, classifica dominio, conta ambiguità strutturali. Se ≥2 → fai UNA domanda bloccante, aspetta.
- PHASE 1 (inference loop, interno): marca ogni assunzione [INFERRED-FROM-TEXT] o [ASSUMED-NO-BASIS]. Controlla compatibilità goal.
- PHASE 2 (Panel of Agents, su ogni file): ARCHITECT blocca contraddizioni strutturali. DESIGNER blocca step senza output concreto o user flow assente. PRAGMATIST blocca se scope>budget+50% o budget senza contingency. CRITIC blocca se <3 failure scenarios o [ASSUMED-NO-BASIS] senza entry in 10_ERROR. BLOCK = rigenera il file.
- PHASE 3: genera 13 file in ordine (00_INDEX → 01_GOAL → 02_PRODUCT → 03_NEXT_STEPS → 04_ELEMENTS → 05_COMPONENTS → 06_PRICE → 07_BUDGET → 08_LIMITS → 09_AGENTS → 10_ERROR → 11_INTERPOLATION → 12_ASKED) in cartella new-ideas/{domain}/{project}/. Ogni file con frontmatter YAML specifico per il progetto.
- PHASE 4: Close Gate — tutti i 13 file presenti, nessun BLOCK irrisolto, 08_LIMITS ha hard limit per ogni skill iniettata, 12_ASKED non ha [ASSUMED-NO-BASIS] senza entry in 10_ERROR.
Per il protocollo completo (Skill Discovery Engine, injection rules, agent specs): https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
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

### Metodo 0 — Copia e incolla (nessun install, funziona ovunque)

Copia il blocco nella sezione ⚡ qui sopra e incollalo come primo messaggio nel chat.

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

Se il tuo agente supporta fetch HTTP, incolla uno di questi URL nel contesto:

```
# Versione compatta (consigliata per incollare):
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL_COMPACT.md

# Versione completa:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/SKILL.md

# Protocollo completo:
https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md
```

### npx (markdownskill.com)

```bash
npx skills add Domenico0976/Ritroso.md
```

---

## How to Use

1. Attiva la skill (uno dei metodi sopra, o copia il blocco ⚡)
2. Fornisci il prompt del progetto al modello
3. Il sistema classifica il dominio automaticamente
4. Lo Skill Discovery Engine trova, scarica o inietta skill rilevanti
5. Se mancano info critiche, riceverai **una domanda alla volta**
6. I 13 file generati vengono salvati in `new-ideas/{domain}/{project}/`
7. `00_INDEX.md` include il **Discovery Log** con ogni metodo usato

---

## Structure

```
Ritroso.md/
├── SKILL.md                    # Entry point completo (agenti con filesystem/fetch)
├── SKILL_COMPACT.md            # Entry point compatto (copia-incolla in chat)
├── skills/
│   ├── PLAN.md                 # Protocollo di esecuzione completo (v1.4)
│   ├── SKILL.md                # Skill definition originale
│   └── context-mapper.yaml     # Skill ausiliaria
├── prompts/
├── new-ideas/                  # Output generati
└── docs/
    ├── RITROSO_DEEP_DIVE.md    # Riferimento tecnico completo (v1.4)
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

- [SKILL_COMPACT.md](./SKILL_COMPACT.md) — versione copia-incolla per attivazione immediata
- [SKILL.md](./SKILL.md) — entry point completo (agenti con fetch/filesystem)
- [skills/PLAN.md](./skills/PLAN.md) — protocollo completo v1.4 (Skill Discovery Engine, injection rules, agent specs)
- [docs/RITROSO_DEEP_DIVE.md](./docs/RITROSO_DEEP_DIVE.md) — riferimento tecnico approfondito

---

## Philosophy

> *"The model must be wrong before it can be right."*

Negative verification is not destructive — it is the mechanism that transforms a mediocre output into a coherent one. The inference loop adds a second layer: **coherence is built before generation, not just verified after**.
