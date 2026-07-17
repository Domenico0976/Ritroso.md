# Ritroso.md

> Skill di ragionamento contestuale per LLM con generazione multimodale
> di set completi di file `.md` e auto-verifica retroattiva.

## Cos'è

**Ritroso.md** è un framework di skill per modelli LLM che forza il modello a:

1. Classificare il tipo di progetto e creare una cartella dedicata
2. Generare un **set completo di 13 file `.md`** come mappa strutturata del progetto
3. Leggere ogni risorsa disponibile nel contesto in modo **multimodale** (testo, codice, immagini, PDF, repo GitHub)
4. Sottoporsi a un **prompt negativo di auto-verifica** — il modello viene attaccato sul suo stesso output e deve difendersi con coerenza cross-file

Il nome "Ritroso" indica il movimento: **il modello va avanti, poi torna indietro a verificare**.

---

## Struttura

```
Ritroso.md/
├── skills/
│   ├── RITROSO.md            # Skill principale — flusso completo (markdown + YAML frontmatter)
│   └── context-mapper.yaml   # Skill ausiliaria — mappatura risorse multimodale
├── prompts/
│   ├── negative-verification.md   # Prompt negativo di auto-verifica
│   ├── project-template.md        # Intestazione standard condivisa dal set
│   └── file-set-templates/        # Template per ognuno dei 13 file del set
│       ├── 00_INDEX.md
│       ├── 01_GOAL.md
│       ├── 02_PRODUCT.md
│       ├── 03_NEXT_STEPS.md
│       ├── 04_ELEMENTS.md
│       ├── 05_COMPONENTS.md
│       ├── 06_PRICE.md
│       ├── 07_BUDGET.md
│       ├── 08_LIMITS.md
│       ├── 09_AGENTS.md
│       ├── 10_ERROR.md
│       ├── 11_INTERPOLATION.md
│       └── 12_ASKED.md
├── new-ideas/                # Output generati — organizzati per domain/progetto
│   └── {domain-slug}/
│       └── {project-slug}/
│           ├── 00_INDEX.md
│           └── ... (13 file totali)
└── docs/
    └── how-it-works.md       # Documentazione tecnica del flusso v2
```

---

## Flusso Rapido v2

```
[PROMPT UTENTE]
      ↓
[SALVA PROMPT INTERO] → .ritroso_prompt_cache.tmp
      ↓
[CLASSIFICA DOMINIO] → domain_slug + project_name_slug
      ↓
[CREA CARTELLA] → new-ideas/{domain_slug}/{project_name_slug}/
      ↓
[CONTEXT CHECK] → manca contesto? → [DOMANDA ESPANSIONE — 1 sola]
      ↓
[CONTEXT MAPPER multimodale] → testo · codice · img · pdf · repo
      ↓
[GENERA 13 FILE .MD] → 01_GOAL → 02_PRODUCT → ... → 12_ASKED
      ↓
[PROMPT NEGATIVO] → "hai sbagliato tutto, verifica cross-file"
      ↓
[00_INDEX.md] + [RITROSO-VERIFIED]
```

---

## I 13 File del Set

| # | File | Risponde a |
|---|------|------------|
| 00 | `INDEX` | Mappa di navigazione — generato per ultimo |
| 01 | `GOAL` | Perché esiste il progetto |
| 02 | `PRODUCT` | Cosa fa nel dettaglio |
| 03 | `NEXT_STEPS` | Cosa fare adesso, ordinato P1/P2/P3 |
| 04 | `ELEMENTS` | Cosa contiene il sistema |
| 05 | `COMPONENTS` | Come è costruito |
| 06 | `PRICE` | Quanto costa per l'utente |
| 07 | `BUDGET` | Quanto costa da produrre |
| 08 | `LIMITS` | Cosa non si può/deve fare |
| 09 | `AGENTS` | Chi fa cosa (AI + umani) |
| 10 | `ERROR` | Cosa può andare storto |
| 11 | `INTERPOLATION` | Come tutto si connette |
| 12 | `ASKED` | Cosa non sappiamo ancora |

---

## Come Usare

1. Carica la skill [`skills/RITROSO.md`](./skills/RITROSO.md) nel tuo sistema di skill
   (es. GitHub Copilot, OpenDevin, Continue, o custom agent)
2. Fornisci un prompt di progetto
3. Il sistema classifica automaticamente il dominio e crea la cartella output
4. Se manca contesto critico, ti verrà posta **una sola domanda** prima di procedere
5. Troverai i 13 file `.md` generati in `new-ideas/{domain}/{progetto}/`
6. L'intero set viene auto-verificato con il prompt negativo prima della consegna

---

## Domain Types Supportati

`design-campaign` · `web-development` · `ai-agent` · `product-strategy`
`content-marketing` · `infrastructure` · `business-plan` · `research` · `generic`

---

## Filosofia

> *"Il modello deve avere torto prima di avere ragione."*

Il prompt negativo non è distruttivo — è il meccanismo che trasforma un output mediocre
in uno coerente. Il LLM viene forzato a confrontare il suo lavoro con il prompt originale
da **angoli ostili** (vendita, tempo, risorse, errori logici, contraddizioni cross-file).

In v2 la verifica è più potente perché controlla la **consistenza tra tutti i 13 file**,
non solo la coerenza di un singolo documento con il prompt.
