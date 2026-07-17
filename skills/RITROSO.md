---
name: ritroso
version: "2.0.0"
author: Domenico Fusto
tags:
  - reasoning
  - context
  - self-verification
  - project-planning
  - multimodal
  - llm-skill
uses:
  - skills/context-mapper.yaml
variables:
  output_base_folder: new-ideas
  temp_prompt_file: .ritroso_prompt_cache.tmp
  negative_prompt_ref: prompts/negative-verification.md
  file_set_ref: prompts/file-set-templates/
---

# RITROSO — Skill di Ragionamento Contestuale v2

> Skill di ragionamento contestuale per LLM con generazione multimodale
> di set completi di file `.md` e auto-verifica retroattiva.

**Quando ricevi un prompt, esegui questi step nell'ordine:**

1. Classifica il tipo di richiesta (domain)
2. Crea una cartella dedicata `new-ideas/{domain_slug}/{project_name_slug}/`
3. Genera un **set completo di 13 file `.md`** dettagliati per quel progetto
4. Ogni file copre una dimensione specifica:
   `GOAL` · `PRODUCT` · `NEXT_STEPS` · `ELEMENTS` · `COMPONENTS` · `PRICE` · `BUDGET` · `LIMITS` · `AGENTS` · `ERROR` · `INTERPOLATION` · `ASKED`
5. Auto-verifica l'intero set con il prompt negativo prima di consegnare l'output

Il sistema è **multimodale**: legge testo, codice, immagini, PDF, video,
repo GitHub e ogni risorsa disponibile nel contesto.

---

## Domain Types

Usati per classificare il dominio e nominare la cartella output.

| Slug | Keywords |
|------|----------|
| `design-campaign` | campagna, grafica, visual, brand, poster, mascotte, layout, UI, UX |
| `web-development` | app, sito, frontend, backend, API, database, Next.js, React, PHP |
| `ai-agent` | agente, agent, orchestrazione, LLM, modello, pipeline, skill |
| `product-strategy` | prodotto, MVP, roadmap, feature, lancio, posizionamento |
| `content-marketing` | contenuto, post, social, SEO, copy, articolo, newsletter |
| `infrastructure` | deploy, docker, CI/CD, cloud, server, DNS, infrastruttura |
| `business-plan` | business, budget, revenue, costi, pricing, investimento |
| `research` | ricerca, analisi, benchmark, studio, confronto, report |
| `generic` | *(fallback se nessun dominio è identificato)* |

---

## Step 1 — Ricezione e Salvataggio Prompt

```yaml
id: receive_prompt
action: capture_input
save_to: .ritroso_prompt_cache.tmp
```

Ricevi il prompt **INTERO** dell'utente e salvalo immutato nel file temporaneo.

- **NON** sintetizzare
- **NON** correggere
- **NON** parafrasare

Il prompt viene riusato letteralmente nel prompt negativo finale.

---

## Step 1b — Classificazione Dominio e Naming Cartella

```yaml
id: classify_domain
depends_on: [receive_prompt]
action: classify
output:
  domain_slug: "{{ classified_domain }}"
  output_folder: "new-ideas/{{ domain_slug }}/{{ project_name_slug }}"
```

Analizza il prompt per identificare il **dominio primario** dalla tabella Domain Types:

- Se il prompt tocca più domini → scegli il più dominante
- Se nessun dominio è chiaro → usa `generic`
- Estrai `project_name_slug` dal prompt: max 3 parole, kebab-case, minuscolo

**Esempio:**
```
prompt: "voglio una campagna per Neeo"
→ domain_slug:    design-campaign
→ output_folder:  new-ideas/design-campaign/neeo-campagna/
```

---

## Step 2 — Verifica Contesto

```yaml
id: context_check
depends_on: [classify_domain]
action: evaluate
evaluation_criteria:
  - domain_identified: bool
  - constraints_known: bool
  - anti_patterns_known: bool
  - resources_available: bool
  - enough_for_full_set: bool
on_incomplete:
  next_step: context_expansion_question
on_complete:
  next_step: map_resources
```

Poniti queste domande interne **obbligatorie**:

- Conosco il dominio? ✓ (già classificato)
- Conosco i limiti tecnici, economici, temporali?
- Conosco le cose da **NON** fare?
- Ho risorse esistenti da leggere? (file `.md`, codice, immagini, PDF, repo)
- Posso stimare budget, agenti, componenti anche approssimativamente?

---

## Step 2.5 — Domanda di Espansione Contesto

```yaml
id: context_expansion_question
action: generate_question
after_answer:
  action: merge_context
  next_step: map_resources
```

Se il contesto è insufficiente, genera **UNA SOLA domanda** — quella più bloccante.

- Focus esclusivo su: funzioni, limiti, vincoli, anti-pattern
- **MAI** chiedere stile, preferenze estetiche o dettagli opzionali

```
Per generare il set completo di file per il tuo progetto ho bisogno
di capire un aspetto critico:

{{ generated_question }}

(Anche una risposta breve è sufficiente.)
```

---

## Step 3 — Mappatura Risorse (Multimodale)

```yaml
id: map_resources
depends_on: [context_check]
action: invoke_skill
skill: context-mapper
output:
  resource_map: "{{ context_mapper.output }}"
```

Invoca `context-mapper` per scansionare **tutte** le risorse disponibili:

| Tipo | Estensioni | Azione |
|------|-----------|--------|
| Testo | `.md` `.txt` `.pdf` | legge e riassume |
| Codice | `.ts` `.js` `.py` `.php` | estrae struttura, funzioni, tipi |
| Config | `.json` `.yaml` `.env` | legge chiavi e dipendenze |
| Immagini | `.png` `.jpg` `.svg` | nota presenza, nome, cartella |
| Video | `.mp4` `.mov` | nota presenza |
| Repo remoti | URL GitHub nel prompt | legge struttura root |

Ogni risorsa viene valutata per rilevanza rispetto al `domain_slug`.

---

## Step 4 — Generazione Set Completo 13 File `.md`

```yaml
id: generate_file_set
depends_on: [map_resources]
action: generate_file_set
output_folder: "{{ output_folder }}"
templates_ref: prompts/file-set-templates/
```

Crea la cartella output e genera tutti i file del set:

| File | Risponde a |
|------|------------|
| `00_INDEX.md` | Mappa navigazione — generato **per ultimo** |
| `01_GOAL.md` | Perché esiste il progetto |
| `02_PRODUCT.md` | Cosa fa nel dettaglio |
| `03_NEXT_STEPS.md` | Cosa fare adesso, ordinato P1/P2/P3 |
| `04_ELEMENTS.md` | Cosa contiene il sistema |
| `05_COMPONENTS.md` | Come è costruito |
| `06_PRICE.md` | Quanto costa per l'utente |
| `07_BUDGET.md` | Quanto costa da produrre |
| `08_LIMITS.md` | Cosa non si può/deve fare |
| `09_AGENTS.md` | Chi fa cosa (AI + umani) |
| `10_ERROR.md` | Cosa può andare storto |
| `11_INTERPOLATION.md` | Come tutto si connette |
| `12_ASKED.md` | Cosa non sappiamo ancora |

### Regole di Generazione

1. Ogni file usa l'intestazione standard di `prompts/project-template.md`
2. Il contenuto è adattato al `domain_slug` classificato
3. Usa il formato più veloce per te stesso: stringhe compresse, simboli `→`, notazioni token-friendly
4. Ogni file contiene `[[wikilink]]` verso gli altri 12 file del set
5. Se una sezione manca di contesto → scrivi `N/A: [motivazione]` + `[OPEN]`
6. Nessuna duplicazione: se il contenuto è già altrove → `→ vedi [[file]]`

### Monologue Interno (attivo per ogni file)

Prima di passare al file successivo chiediti:

- Il file appena scritto è coerente con `01_GOAL.md`?
- Sto contraddicendo qualcosa in un file precedente?
- I `[[wikilink]]` puntano a file che esistono nel set?

→ Se sì a qualsiasi problema: correggi **inline** prima di continuare.

---

## Step 5 — Prompt Negativo di Auto-Verifica

```yaml
id: negative_verification
depends_on: [generate_file_set]
action: self_verify
prompt_ref: prompts/negative-verification.md
inject_original_prompt: true
inject_full_file_set: true
verification_axes:
  - coherence_with_prompt
  - cross_file_consistency
  - logical_consistency
  - resource_feasibility
  - time_impact
  - business_impact
  - anti_pattern_violations
  - missing_critical_sections
  - wikilinks_validity
```

Applica il prompt negativo sull'**intero file set**.
**Parti dall'assunto che HAI SBAGLIATO.**

Verifica ogni file su questi assi:

- Coerenza con il prompt utente originale (intero, dal `.tmp`)
- Consistenza interna cross-file tra tutti i 13 documenti
- Fattibilità delle risorse dichiarate
- Impatto reale: tempo · vendita · sviluppo · vita dell'utente
- Anti-pattern violati
- File con `N/A` non giustificato
- Wikilink che puntano a file inesistenti

→ Se trova errori: correggi il file incriminato e loga `[CORRECTION]`  
→ Se tutto regge: marca `[RITROSO-VERIFIED]` sull'intera cartella

---

## Step 6 — Output Finale

```yaml
id: final_output
depends_on: [negative_verification]
action: finalize
generate_index: true
index_file: 00_INDEX.md
cleanup:
  - .ritroso_prompt_cache.tmp
mark_verified: true
verified_tag: "[RITROSO-VERIFIED]"
```

Finalizza l'output:

1. Aggiungi `[RITROSO-VERIFIED]` all'intestazione di ogni file del set
2. Genera `00_INDEX.md` con lista file + conteggio correzioni + domande aperte
3. Pulisci `.ritroso_prompt_cache.tmp`
4. Restituisci il percorso della cartella generata

---

## Filosofia

> *«Il modello deve avere torto prima di avere ragione.»*

In v2 il prompt negativo verifica la **consistenza cross-file** tra tutti
i 13 documenti — non solo la coerenza di un singolo file con il prompt.
Il sistema non è mai fiducioso: ogni output è guilty until proven innocent.
