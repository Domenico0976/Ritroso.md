# Come Funziona Ritroso.md — v2

## Cosa è cambiato dalla v1

La v1 generava un singolo file `.md` temporaneo.
La **v2** genera un **set completo di 13 file `.md`** (00_INDEX + 12 file tematici)
in una cartella dedicata, organizzata automaticamente per tipo di progetto.
Il sistema è ora **multimodale**: legge testo, codice, immagini, PDF, video
e repo remoti per costruire il contesto prima di generare.

---

## Il Flusso Completo v2

```
[PROMPT UTENTE]
      ↓
[SALVA PROMPT INTERO] → .ritroso_prompt_cache.tmp
      ↓
[CLASSIFICA DOMINIO] → domain_slug + project_name_slug
      ↓
[CREA CARTELLA] → new-ideas/{domain_slug}/{project_name_slug}/
      ↓
[CONTEXT CHECK] → ha abbastanza per il file set?
      ↓ NO              ↓ SÌ
[DOMANDA UTENTE]  [CONTEXT MAPPER — multimodale]
      ↓                 ↓ testo + codice + img + pdf + repo
[MERGE CONTEXT]   [GENERA 13 FILE .MD]
      ↓                 ↓ 00_INDEX → 01_GOAL → ... → 12_ASKED
      └───────────────→↓
                  [PROMPT NEGATIVO]
                        ↓ "hai sbagliato tutto, dimostralo"
                  [CORREZIONI + VERIFICA CROSS-FILE]
                        ↓
                  [00_INDEX.md finale] + [RITROSO-VERIFIED]
```

---

## I 13 File del Set

| # | File | Risponde a |
|---|------|------------|
| 00 | INDEX | Mappa di navigazione dell'intero set |
| 01 | GOAL | Perché esiste il progetto |
| 02 | PRODUCT | Cosa fa nel dettaglio |
| 03 | NEXT_STEPS | Cosa fare adesso |
| 04 | ELEMENTS | Cosa contiene il sistema |
| 05 | COMPONENTS | Come è costruito |
| 06 | PRICE | Quanto costa per l'utente |
| 07 | BUDGET | Quanto costa da produrre |
| 08 | LIMITS | Cosa non si può/deve fare |
| 09 | AGENTS | Chi fa cosa (AI + umani) |
| 10 | ERROR | Cosa può andare storto |
| 11 | INTERPOLATION | Come tutto si connette |
| 12 | ASKED | Cosa non sappiamo ancora |

---

## Struttura Cartelle Output

```
new-ideas/
├── design-campaign/
│   └── neeo-campagna/
│       ├── 00_INDEX.md
│       ├── 01_GOAL.md
│       ├── 02_PRODUCT.md
│       └── ... (13 file totali)
├── web-development/
│   └── nome-progetto/
│       └── ...
├── ai-agent/
├── product-strategy/
├── content-marketing/
├── infrastructure/
├── business-plan/
└── research/
```

---

## Modalità Multimodale

Il `context-mapper` v2 processa:

| Tipo | Estensioni | Azione |
|------|-----------|--------|
| Testo | `.md` `.txt` `.pdf` | legge e riassume |
| Codice | `.ts` `.js` `.py` `.php` | estrae struttura |
| Config | `.json` `.yaml` `.env` | legge chiavi |
| Immagini | `.png` `.jpg` `.svg` | nota presenza |
| Video | `.mp4` `.mov` | nota presenza |
| Repo remoti | URL GitHub nel prompt | legge root |

---

## Wikilinks tra File

Ogni file del set contiene `[[wikilink]]` verso gli altri 12.
Questo permette:
- Navigazione in Obsidian o qualsiasi viewer MD con wikilinks
- Il modello verifica durante il prompt negativo che i link siano validi
- Knowledge graph navigabile del progetto auto-generato

---

## Domain Types

| Slug | Keywords principali |
|------|--------------------|
| `design-campaign` | campagna, grafica, visual, brand, UI, UX |
| `web-development` | app, sito, API, database, Next.js, React |
| `ai-agent` | agente, LLM, orchestrazione, pipeline |
| `product-strategy` | MVP, roadmap, feature, lancio |
| `content-marketing` | post, social, SEO, copy, newsletter |
| `infrastructure` | deploy, docker, CI/CD, cloud, DNS |
| `business-plan` | budget, revenue, pricing, investimento |
| `research` | analisi, benchmark, confronto, report |
| `generic` | fallback |

---

## Filosofia Invariata

> *"Il modello deve avere torto prima di avere ragione."*

In v2 il prompt negativo è più potente perché verifica
la **consistenza cross-file** tra tutti i 13 documenti,
non solo la coerenza di un singolo file con il prompt.
