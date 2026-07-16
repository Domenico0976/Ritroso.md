# Come Funziona Ritroso.md

## Il Problema che Risolve

I modelli LLM tendono a generare output "plausibili" invece di output "coerenti".
La differenza:
- **Plausibile**: sembra giusto, segue pattern comuni
- **Coerente**: è giusto per QUESTO contesto, QUESTO utente, QUESTI vincoli

Ritroso.md forza il modello a passare dal plausibile al coerente.

---

## Il Flusso in Dettaglio

### Fase 1 — Acquisizione Sicura del Prompt

Il prompt utente viene salvato **immutato** in `.ritroso_prompt_cache.tmp`.
Questo è critico: il prompt originale viene usato nel prompt negativo finale
e NON deve essere sintetizzato, parafrasato o modificato in nessun modo.

```
PROMPT UTENTE → [salva intero] → .ritroso_prompt_cache.tmp
```

### Fase 2 — Context Check

Il modello valuta 4 dimensioni:
- Dominio noto? (dev/design/business/time)
- Vincoli noti? (tecnici/economici/temporali)
- Anti-pattern noti? (cosa non fare)
- Risorse disponibili? (file esistenti nel workspace)

Se anche solo UNA dimensione critica manca → domanda al utente.
UNA sola domanda, quella più bloccante.

### Fase 3 — Context Mapping

La skill `context-mapper` scansiona il workspace.
Non è una semplice lista di file — è una valutazione di rilevanza:
- Questi file contraddicono il prompt?
- Questi file aggiungono vincoli impliciti?
- Manca qualcosa che ci si aspetterebbe?

### Fase 4 — Generazione .md Temporaneo

Il file viene generato secondo `project-template.md`.
Il modello mantiene un **loop interno di coerenza** durante la generazione:
```
per ogni sezione generata:
  → "È coerente con il prompt?"
  → "Aggiunge complessità inutile?"
  → "Manca qualcosa di critico?"
  se sì a qualsiasi → correggi inline
```

Il formato può essere non-umano se accelera il ragionamento del modello.

### Fase 5 — Prompt Negativo (il cuore del sistema)

Questo è il meccanismo distintivo di Ritroso.md.

Il modello riceve `negative-verification.md` con il prompt utente iniettato.
Parte dall'assunto che **ha sbagliato tutto**.

Verifica 5 assi:
1. Coerenza logica con il prompt
2. Impatto reale (tempo, vendita, risorse, sviluppo)
3. Peso sulla vita dell'utente
4. Anti-pattern violati
5. Domande aperte non risolte

Se trova errori → corregge e loga con `[CORRECTION]`.
Se regge → marca con `[RITROSO-VERIFIED]`.

### Fase 6 — Output Validato

File finale in `new-ideas/` marcato come `[RITROSO-VERIFIED]`.
Riepilogo correzioni se ce ne sono.
Domande ancora aperte segnalate con `[OPEN]`.

---

## Perché "Ritroso"?

Il modello fa un percorso in avanti (genera) e poi torna indietro (verifica).
Il movimento "ritroso" non annulla il lavoro — lo valida.

È ispirato al concetto di **backward induction** nella teoria dei giochi:
per trovare la strategia ottimale, parti dal risultato finale e vai a ritroso
per capire se ogni passo che ti ha portato lì era corretto.

---

## Integrazione con Sistemi Esistenti

Ritroso.md è progettato per essere compatibile con:
- **GitHub Copilot** (skill format)
- **Continue.dev** (custom prompts)
- **OpenDevin / SWE-agent** (action sequences)
- **Custom agent pipelines** (YAML action chains)
- **LangChain / CrewAI** (come step in una chain)

La struttura YAML è deliberatamente generica per massimizzare la portabilità.
