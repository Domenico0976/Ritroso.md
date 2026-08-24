# Ritroso.md — Documentazione Tecnica Approfondita

Questo documento descrive in dettaglio ogni componente del protocollo Ritroso, dalla struttura del file-set al motore di Skill Discovery, dal Panel of Agents alle regole di injection. È la referenza completa per chi vuole capire come funziona ogni decisione implementativa.

---

## 1. Architettura del Protocollo

Ritroso è un **protocollo di esecuzione deterministico** per agenti LLM. Non è una libreria, un tool, o un'API — è un insieme di istruzioni operative che l'agente segue in ordine fisso, senza possibilità di saltare fasi o modificare la sequenza.

Il protocollo è articolato in 5 fasi:

```
PHASE 0  → Intake, classificazione dominio, conteggio ambiguità, Skill Discovery
PHASE 1  → Inference Loop: 13 domande inter-file prima di scrivere
PHASE 2  → Panel of Agents: 4 agenti validano ogni file
PHASE 3  → File Generation: 13 file in ordine fisso
PHASE 4  → Close Gate: 8 condizioni di chiusura
```

### Principio di non-blocking

Nessun fallimento nella discovery delle skill blocca la generazione. Se tutte le skill sono assenti → si genera comunque, con raccomandazioni di install in `00_INDEX.md`. Questo garantisce che Ritroso produca sempre output, anche in ambienti con filesystem inaccessibile o senza connessione di rete.

### Principio di tracciabilità

Ogni regola iniettata da una skill esterna porta un tag esplicito (`[SKILL:nome]` o `[SKILL:nome:remote-fetch]`). Questo rende l'output **auditabile**: è sempre possibile sapere quale regola viene da quale fonte.

---

## 2. PHASE 0 — Prompt Intake & Classification

### §0.1 — Lettura letterale del prompt

Il prompt viene letto esattamente come fornito. L'agente **non migliora**, non parafrasa, non aggiunge termini tecnici. Il testo grezzo dell'utente è la ground truth assoluta.

Questa regola esiste per prevenire il fenomeno di "intent drift": gli LLM tendono ad assumere che l'utente intenda qualcosa di più sofisticato di ciò che ha scritto, portando a file-set che non corrispondono al progetto reale.

### §0.2 — Classificazione dominio

Il progetto viene assegnato a uno di sei domini:

| Dominio | Esempi |
|---------|--------|
| `software-product` | App, SaaS, tool, API |
| `creative-technical` | Audiovisivo, generative art, installazione interattiva |
| `content-operations` | Editoriale, CMS, workflow, publishing |
| `open-source-framework` | Libreria, skill, framework di prompting, dev tool |
| `service-or-agency` | Deliverable consulenza, progetto cliente |
| `other` | Documentato esplicitamente |

La classificazione del dominio è input diretto per il **Method C (remote-fetch)**: determina quali categorie di skill vengono fetchate automaticamente in caso di discovery vuota.

### §0.3 — Conteggio ambiguità e GATE 0

Un'**ambiguità strutturale** è qualsiasi gap che cambia architettura, pricing, scope, o distribuzione del progetto. Se il conteggio raggiunge 2 o più, GATE 0 blocca la generazione.

La regola critica: l'agente pone **una sola domanda**, quella che sblocca il maggior numero di altre risposte. Non un elenco di domande. Questo principio — chiamato "domanda di massima leva" — riduce la frizione con l'utente e mantiene il dialogo scorrevole.

---

## 3. PHASE 0.4 — Skill Discovery & Injection Engine

### Architettura del motore

Il motore di discovery è il componente più sofisticato del protocollo. Opera in cascata su 5 metodi, ognuno attivato solo se il precedente non ha prodotto risultati sufficienti.

```
Method 1 (Context)
    ↓ [0 risultati]
Method 2 (Direct path scan)
    ↓ [0 risultati o access denied]
Method 3 (Grep fallback)
    ↓ [0 risultati o no shell access]
Method C (Remote fetch)        ← NUOVO in v1.4
    ↓ [0 risultati o no HTTP]
Method 4 (Inference)
```

### Method 1 — Context Scan

Questo è il metodo a costo zero: nessun accesso al filesystem, nessuna chiamata HTTP. L'agente scansiona il contesto della conversazione corrente cercando:

- File `SKILL.md` già caricati dall'utente
- Nomi di skill menzionati esplicitamente ("usa impeccable", "ponytail è attivo")
- Chiamate `load_skill()` già eseguite nella sessione
- Blocchi YAML frontmatter con campo `name:` che sembrano dichiarazioni di skill

Qualsiasi skill trovata via Method 1 è immediatamente `ACTIVE` — le sue regole vengono iniettate senza ulteriori verifiche.

### Method 2 — Direct Path Scan

Scansione di **tutti i path noti** per ogni sistema operativo, compresi i path di agenti alternativi (Codex CLI, Gemini CLI, OpenCode, Qwen Code). Il motore **non si ferma al primo path trovato**: continua a controllare tutti i path anche dopo aver trovato skill, per rilevare skill in posizioni multiple.

Per ogni directory trovata:
1. Lista tutte le sottodirectory
2. Cerca `SKILL.md` (case-insensitive) dentro ognuna
3. Se trovato → legge il file, estrae `name`, `description`, `version` dal YAML frontmatter
4. Marca come `ACTIVE`

**Path Windows scansionati:**
```
%USERPROFILE%\.claude\skills\    %APPDATA%\Claude\skills\
%LOCALAPPDATA%\Claude\skills\   %USERPROFILE%\skills\
%USERPROFILE%\.codex\skills\    %USERPROFILE%\.gemini\skills\
%USERPROFILE%\agent-skills\     %USERPROFILE%\llm-skills\
```

**Path macOS scansionati:**
```
~/.claude/skills/
~/Library/Application Support/Claude/skills/
~/.codex/skills/   ~/.gemini/skills/   ~/.opencode/skills/
~/agent-skills/    ~/llm-skills/
```

**Path Linux scansionati (XDG standard):**
```
~/.config/claude/skills/   ~/.local/share/claude/skills/
~/.claude/skills/          ~/skills/   ~/claude/skills/
~/.codex/skills/           ~/.gemini/skills/
```

### Method 3 — Grep Fallback

Attivato quando il path scan fallisce (access denied o path inesistenti). L'agente esegue comandi `find` (macOS/Linux) o `Get-ChildItem` (Windows PowerShell) dal project root e dalla home directory. Dopo aver trovato i file, li filtra con grep per verificare che contengano `name:` e `description:` nel frontmatter — evitando di trattare come skill qualsiasi file chiamato `SKILL.md` per coincidenza.

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

Questo è il metodo più innovativo della v1.4. Quando tutti i metodi locali falliscono, l'agente **non aspetta che l'utente installi le skill** — le fetcha autonomamente.

**Comportamento dettagliato:**

1. **Silenzioso**: nessun prompt all'utente prima del fetch
2. **Scoped**: fetch solo delle categorie rilevanti per il dominio classificato in §0.2 (max 5 fetch per run)
3. **Prioritizzato**: per ogni categoria, viene fetchata la prima skill nella tabella del catalogo §0.4.2
4. **Non permanente**: nessun file viene scritto su disco. Le regole sono iniettate solo per la generazione corrente
5. **Loggato**: ogni tentativo (successo o fallimento) viene registrato nel Discovery Log di `00_INDEX.md`
6. **Resiliente**: se un fetch fallisce (HTTP error, timeout) → skip silenzioso, la generazione non si blocca

**Template di esecuzione:**

```
FOR each project-relevant category (max 5):
  url = primo raw URL dal catalogo §0.4.2 per quella categoria
  response = HTTP GET url
  IF response.status == 200:
    content = response.body
    verifica frontmatter YAML (name: + description:)
    estrai regole operative
    mark as ACTIVE (source: remote-fetch)
    inietta con tag [SKILL:nome:remote-fetch]
    log: { skill, url, status: "fetched", method: "remote-fetch" }
  ELSE:
    log: { skill, url, status: "fetch-failed", http: status_code }
    skip silenzioso
```

**Differenza chiave rispetto a v1.3**: in v1.3 una skill ABSENT produceva solo raccomandazioni nell'INDEX. In v1.4, se il raw URL è noto nel catalogo, l'agente può **bypassare completamente l'install** e arricchire la generazione comunque.

### Method 4 — Inference

Ultimo fallback assoluto. L'agente cerca tracce di skill in file di configurazione (`CLAUDE.md`, `package.json`, `pyproject.toml`) o commenti nel codice. Le skill trovate così vengono classificate come `PROBABLE` — non `ACTIVE` — perché il contenuto non è stato letto, solo il nome è stato inferito.

### Classificazione dei risultati

| Status | Significato | Azione |
|--------|-------------|--------|
| `ACTIVE` | SKILL.md letto da disco o da contesto | Inietta regole nei file target |
| `ACTIVE (remote-fetch)` | SKILL.md fetchato da raw URL, non installato | Inietta con tag `:remote-fetch`; suggerisci install nell'INDEX |
| `PROBABLE` | Nome trovato per inferenza, contenuto non letto | Lista nell'INDEX, segnala come non verificato |
| `ABSENT` | Non trovato da nessun metodo (locale o remoto) | Raccomanda nell'INDEX con istruzioni di install |

---

## 4. Catalogo Skill — 13 Categorie

Il catalogo §0.4.2 copre lo spettro completo dallo sviluppo alla sicurezza al marketing. Ogni categoria specifica: quali skill la coprono, quali file Ritroso ricevono injection, e cosa viene iniettato.

| Categoria | Injection Target principali |
|-----------|--------------------------|
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

**Fonti del catalogo:**
- Perplexity built-in: `chart`, `website-building`
- Anthropic built-in: `doc`, `pdf`, `pptx`, `xlsx`
- Esterne: [Prat011/awesome-llm-skills](https://github.com/Prat011/awesome-llm-skills), [mingrath/awesome-claude-skills](https://github.com/mingrath/awesome-claude-skills), [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills), [gmh5225/awesome-skills](https://github.com/gmh5225/awesome-skills)

### Priorità utente vs catalogo

Se l'utente ha una skill custom che copre la stessa categoria di una skill del catalogo → la skill dell'utente ha **precedenza assoluta**. La skill del catalogo non viene nemmeno raccomandata per quella categoria. Le skill custom vengono taggate `[SKILL:custom/nome-skill]`.

---

## 5. Injection Rules — Come funziona l'injection

### Regola 1 — Inject rules, not pointers
Mai scrivere "vedi skill X per le regole di design". Le regole operative vengono estratte e scritte **inline** nel file target sotto `## Injected Rules — [nome-skill]`. Il file deve essere autocontenuto.

### Regola 2 — Never block on absent skill
Una skill assente non blocca mai la generazione. Produce solo una voce nel `00_INDEX.md` sotto `## Skill Stack — Recommended` con le istruzioni di install.

### Regola 3 — Tagging obbligatorio
Ogni regola iniettata porta il tag `[SKILL:nome]` per le skill locali, `[SKILL:nome:remote-fetch]` per quelle fetchate da URL. Questo garantisce tracciabilità completa nell'output.

### Regola 4 — Hard Limits automatici
Qualsiasi regola iniettata in `08_LIMITS.md` diventa automaticamente un **Hard Limit** — indipendentemente dalla classificazione che aveva nella skill sorgente. Non ci sono eccezioni.

### Regola 5 — Conflitti espliciti
Se due skill iniettate hanno regole in conflitto tra loro → il conflitto viene flaggato come `[SKILL-CONFLICT: skill-a vs skill-b]` in `11_INTERPOLATION.md` e surfacato all'utente in `12_ASKED.md`. Mai risolto silenziosamente.

### Regole 11 e 12 — Trasparenza e scoping del remote-fetch (v1.4)
- **Regola 11**: Ogni tentativo di remote-fetch (successo o fallimento) deve essere loggato nel Discovery Log. Mai usare contenuto remote-fetched senza registrarlo.
- **Regola 12**: Le regole iniettate via remote-fetch sono valide **solo per la generazione corrente**. Per renderle permanenti, l'utente deve installare la skill via Method A o B.

### Active Agent Install Protocol (v1.4)

Quando l'agente ha accesso shell E una skill è ABSENT ma rilevante per il progetto:

1. Sceglie Method B (curl singolo SKILL.md) come default
2. Esegue il comando di install per l'OS rilevato
3. **Ri-esegue §0.4.0** dopo l'install per rilevare la skill appena installata
4. Logga: `{ skill, method: "agent-installed", path }`
5. Se l'install fallisce → fallback su Method C per la generazione corrente + istruzioni manuali nell'INDEX

---

## 6. PHASE 1 — Inference Loop

Prima di scrivere qualsiasi file, l'agente esegue internamente 12 domande di inferenza — una per ogni file del set (escluso `00_INDEX.md`). Queste domande rilevano contraddizioni, assunzioni non documentate, e conflitti tra obiettivi prima che diventino problemi nell'output.

### Classificazione delle assunzioni

Ogni assunzione in `12_ASKED.md` deve portare uno di due tag:

- `[INFERRED-FROM-TEXT]` — supportata da qualcosa nel prompt originale
- `[ASSUMED-NO-BASIS]` — riempie un gap senza supporto testuale

Le assunzioni `ASSUMED-NO-BASIS` sono automaticamente segnalate anche in `10_ERROR.md` come rischi ad alta incertezza.

### Verifica di compatibilità degli obiettivi

Passo esplicito: *"Gli obiettivi in `01_GOAL` sono mutuamente compatibili?"* — se viene trovato un conflitto → flaggato in `11_INTERPOLATION.md` come `[GOAL-CONFLICT]` con descrizione esplicita.

---

## 7. PHASE 2 — Panel of Agents

Ogni file generato passa attraverso 4 agenti prima di essere chiuso. Se un agente solleva un `BLOCK`, il file viene **rigenerato** — non annotato. Un file con BLOCK aperto non può essere chiuso.

### 🏛 ARCHITECT

**Domanda guida**: *"Questa decisione regge se il progetto raddoppia di scope, cambia piattaforma, o cambia utente target?"*

Blocca se:
- Un componente contraddice `08_LIMITS` (incluse le regole `[SKILL:*]` iniettate)
- I next steps P1 includono infrastruttura che richiede P2
- Una skill di sicurezza iniettata richiede un componente P1 assente da `04_ELEMENTS`

### 🎨 DESIGNER

**Domanda guida**: *"Una persona che non ha scritto questo prompt può leggere questo file e sapere esattamente cosa fare dopo?"*

Blocca se:
- `03_NEXT_STEPS` contiene un passo senza output concreto
- `02_PRODUCT` non ha un user flow (minimo 3 passi)
- `09_AGENTS` lista un ruolo senza responsabilità concreta

### ⚙️ PRAGMATIST

**Domanda guida**: *"È effettivamente buildabile con il tempo, il budget e le persone dichiarate?"*

Blocca se:
- Lo scope P1 supera il budget di >50%
- Un componente richiede expertise non presente in `09_AGENTS`
- `06_PRICE` e `07_BUDGET` sono incoerenti
- Un qualsiasi step P1 dipende da un OPEN non risolto

### 🔒 CRITIC (potenziato in v1.4)

**Domanda guida**: *"Qual è il modo più probabile in cui questo fallisce nei primi 30 giorni?"*

Blocca se:
- `10_ERROR` ha meno di 3 scenari di fallimento
- Una skill di sicurezza è iniettata ma nessun security owner è nominato in `09_AGENTS`
- **[NUOVO v1.4]** Una regola `[SKILL:*:remote-fetch]` copre un hard limit non presente in `08_LIMITS`

---

## 8. PHASE 3 — File Generation

I file vengono generati sempre in ordine fisso:

```
1.  00_INDEX.md      ← Include Skill Discovery Log
2.  01_GOAL.md
3.  02_PRODUCT.md
4.  03_NEXT_STEPS.md
5.  04_ELEMENTS.md
6.  05_COMPONENTS.md ← Include regole iniettate dalle skill
7.  06_PRICE.md
8.  07_BUDGET.md
9.  08_LIMITS.md     ← Ogni regola iniettata diventa Hard Limit
10. 09_AGENTS.md
11. 10_ERROR.md
12. 11_INTERPOLATION.md ← Flagga SKILL-CONFLICT e GOAL-CONFLICT
13. 12_ASKED.md     ← Tutte le assunzioni taggate
```

L'ordine non è arbitrario: ogni file costruisce su quelli precedenti. Dopo ogni file, l'agente esegue un self-check: *"Questo file contraddice qualsiasi file già scritto?"*. Se sì → risolve prima di procedere.

---

## 9. PHASE 4 — Close Gate

La generazione non è completa finché tutte le condizioni non passano:

- [ ] Tutti i 13 file generati
- [ ] `00_INDEX.md` include il Discovery Log con metodo usato e tutti i tentativi di fetch/install
- [ ] Ogni skill ACTIVE (locale o remote-fetch) ha regole iniettate in almeno un file target
- [ ] Ogni skill ABSENT raccomandata ha istruzioni di install in `00_INDEX.md`
- [ ] `08_LIMITS.md` contiene almeno un hard limit per ogni skill iniettata
- [ ] `11_INTERPOLATION.md` flagga eventuali `[SKILL-CONFLICT]` o `[GOAL-CONFLICT]`
- [ ] `12_ASKED.md` non ha item `ASSUMED-NO-BASIS` non presenti anche in `10_ERROR`
- [ ] Il Panel of Agents non ha BLOCK aperti

---

## 10. Il Discovery Log in 00_INDEX.md

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

### v1.4 (agosto 2026)

1. **Method C — Remote Fetch** in §0.4.0: discovery autonoma via HTTP fetch dei raw URL del catalogo. Tag `[SKILL:nome:remote-fetch]` per tracciabilità.
2. **Active Agent Install Protocol** in §0.4.3: install autonoma via curl, re-run discovery, fallback su Method C.
3. **§0.4.4 regole 11 e 12**: ogni fetch loggato; remote-fetch è generation-scoped.
4. **CRITIC agent**: nuovo BLOCK per `[SKILL:*:remote-fetch]` su hard limit non in `08_LIMITS`.
5. **PHASE 3 e PHASE 4** documentate esplicitamente nel PLAN.md.
6. **Discovery Log template** aggiornato con sezioni remote-fetch e agent-install.
7. **Status `ACTIVE (remote-fetch)`** aggiunto alla tabella di classificazione.

### v1.3

- Discovery cross-platform completa (Windows/macOS/Linux + agent alternativi)
- Catalogo 13 categorie, 50+ skill con raw URL diretti
- 4 metodi di discovery prioritizzati
- Injection rules §0.4.4 (regole 1–10)
- Discovery Log template in `00_INDEX.md`
- User skill priority
