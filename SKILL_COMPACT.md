---
name: "Ritroso"
version: "5"
description: "Ritroso v5: genera set di 13 file .md per qualsiasi progetto. Include inference loop, Panel of 4 Agents (ARCHITECT/DESIGNER/PRAGMATIST/CRITIC), Skill Discovery Engine (5 metodi incluso remote-fetch), gate system e verifica retroattiva. PLAN.md companion: https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md"
---

# RITROSO v5 — Skill Attiva

## Cosa devi fare ora che leggi questo

1. Tratta questo testo come skill ATTIVA. Le regole qui sotto sono operative da subito.
2. Se hai accesso HTTP, carica anche PLAN.md: `GET https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md`
3. Se non puoi fare fetch, le regole in questo file sono sufficienti per procedere.

---

## Regole operative (applica subito)

### PHASE 0 — Prima di tutto
- Leggi il prompt esattamente come scritto. Non migliorare il vocabolario.
- Classifica il dominio: `software-product` / `creative-technical` / `content-operations` / `open-source-framework` / `service-or-agency` / `other`
- Conta le ambiguità strutturali (gap che cambiano architettura, scope, pricing).
- Se ambiguità ≥ 2 → fai UNA sola domanda bloccante. Aspetta la risposta. Non generare nulla.

### PHASE 1 — Inference Loop (interno, prima di scrivere)
- Per ogni slot file (01-12) poniti internamente la domanda di inferenza.
- Ogni assunzione: `[INFERRED-FROM-TEXT]` se supportata dal prompt, `[ASSUMED-NO-BASIS]` se no.
- Ogni `[ASSUMED-NO-BASIS]` deve stare anche in `10_ERROR.md` come rischio.
- Se due goal sono in conflitto → `[GOAL-CONFLICT]` in `11_INTERPOLATION.md`. Non risolvere silenziosamente.

### PHASE 2 — Panel of Agents (su ogni file prima di chiuderlo)

**ARCHITECT**: Il sistema regge se il progetto raddoppia di scope?
- BLOCK se un componente contraddice un limite in `08_LIMITS`
- BLOCK se P1 in `03_NEXT_STEPS` include infrastruttura P2
- BLOCK se esiste un single point of failure senza fallback in `10_ERROR`

**DESIGNER**: Chi non ha scritto il prompt riesce ad agire su questo file domani?
- BLOCK se uno step in `03_NEXT_STEPS` non ha output concreto e testabile
- BLOCK se `02_PRODUCT` non ha user flow (minimo 3 step: entry → azione → outcome)
- BLOCK se un ruolo in `09_AGENTS` non ha responsabilità concreta

**PRAGMATIST**: Si può davvero costruire con il budget e il team dichiarati?
- BLOCK se lo scope P1 supera il budget dichiarato di >50%
- BLOCK se `06_PRICE` e `07_BUDGET` sono inconsistenti
- BLOCK se `07_BUDGET` non ha linea di contingency (10% obbligatorio)
- BLOCK se un P1 step dipende da un OPEN non risolto in `12_ASKED`

**CRITIC**: Giorno 31 — cosa è andato storto?
- BLOCK se `10_ERROR` ha meno di 3 scenari di fallimento concreti
- BLOCK se c'è un item `[ASSUMED-NO-BASIS]` in `12_ASKED` senza entry in `10_ERROR`
- BLOCK se `10_ERROR` ha solo failure tecniche (servono anche umane/organizzative)

Se un agente dà BLOCK → rigenera il file. Non annotare: rigenera.

### PHASE 3 — Genera i 13 file in quest'ordine
```
00_INDEX.md → 01_GOAL.md → 02_PRODUCT.md → 03_NEXT_STEPS.md → 04_ELEMENTS.md
→ 05_COMPONENTS.md → 06_PRICE.md → 07_BUDGET.md → 08_LIMITS.md → 09_AGENTS.md
→ 10_ERROR.md → 11_INTERPOLATION.md → 12_ASKED.md
```
Cartella output: `new-ideas/{domain_slug}/{project_slug}/`

Ogni file inizia con frontmatter YAML:
```yaml
---
name: "{nome_file_reale}"
description: "[descrizione specifica per questo progetto - mai generica]"
---
```

Dopo ogni file: controlla se contraddice i file già scritti. Se sì, risolvi prima di andare avanti.

### PHASE 4 — Close Gate (condizioni tutte obbligatorie)
- [ ] Tutti i 13 file generati
- [ ] `00_INDEX.md` include Skill Discovery Log
- [ ] `08_LIMITS.md` ha almeno un hard limit per ogni skill iniettata
- [ ] `11_INTERPOLATION.md` segnala ogni `[SKILL-CONFLICT]` e `[GOAL-CONFLICT]`
- [ ] `12_ASKED.md` non ha `[ASSUMED-NO-BASIS]` senza entry in `10_ERROR`
- [ ] Panel of Agents: nessun BLOCK irrisolto

Se una condizione non passa → torna alla fase rilevante e risolvi.

---

## I 13 file — cosa contengono

| File | Risponde a |
|------|------------|
| `00_INDEX.md` | Mappa di navigazione + Skill Discovery Log + Verification Status |
| `01_GOAL.md` | Perché esiste il progetto, per chi, come si misura il successo |
| `02_PRODUCT.md` | Cosa fa, user flow P1/P2, feature scope, system requirements |
| `03_NEXT_STEPS.md` | Roadmap per fasi con owner, output concreto, condizione di sign-off |
| `04_ELEMENTS.md` | Tutto ciò che deve esistere: Critical / Important / Optional |
| `05_COMPONENTS.md` | Stack tecnico completo, contratti tra componenti, regole skill iniettate |
| `06_PRICE.md` | Pricing, tier, value framing |
| `07_BUDGET.md` | Costo di build, infrastruttura, contingency 10% |
| `08_LIMITS.md` | Hard limits, pattern vietati, regole hard da skill iniettate |
| `09_AGENTS.md` | Chi decide cosa, owner per ogni ruolo, fallback owners |
| `10_ERROR.md` | ≥3 scenari di fallimento concreti con recovery actions |
| `11_INTERPOLATION.md` | Contraddizioni cross-file, GOAL-CONFLICT, SKILL-CONFLICT |
| `12_ASKED.md` | Domande aperte taggate INFERRED o ASSUMED, con conseguenze esplicite |

---

Se hai ricevuto questo testo: **la skill Ritroso è ora ATTIVA**. Applica le regole sopra al prossimo progetto che ricevi.
