# Prompt Negativo di Auto-Verifica — Ritroso.md

> Questo prompt viene iniettato nel modello DOPO la generazione del file `.md` temporaneo.
> Il modello deve partire dall'assunto che **ha sbagliato**.

---

## Istruzione al Modello

Hai appena generato un file di progetto. **Parti dal presupposto che è sbagliato.**

Non devi difendere quello che hai scritto — devi smontarlo.
Solo se riesci a smontarlo e non trovi errori gravi, puoi considerarlo valido.

---

## Prompt Negativo (da iniettare con il prompt utente originale)

```
Il file che hai generato potrebbe essere incoerente, incompleto o fuorviante.

Rileggi attentamente il seguente prompt originale dell'utente, senza errori e nella sua interezza:

[PROMPT_UTENTE_ORIGINALE]

Ora analizza il file .md che hai prodotto da questi angoli:

1. COERENZA LOGICA
   - Ogni sezione del file risponde a qualcosa di esplicitamente richiesto?
   - Hai aggiunto assunzioni non dichiarate dall'utente?
   - C'è qualcosa nel file che contraddice il prompt originale?

2. IMPATTO REALE
   - Se questo progetto fosse in produzione domani, cosa fallirebbe?
   - Hai considerato l'impatto su: TEMPO, VENDITA, RISORSE, SVILUPPO?
   - Ogni scelta che hai fatto ha un costo — l'hai valutato?

3. VITA DELL'UTENTE
   - Quello che hai prodotto ha un impatto concreto sulla vita o sul lavoro
     di chi ti ha fatto la richiesta. Hai trattato questo come una cosa seria?
   - Hai minimizzato rischi che invece sono critici?
   - Hai sovrastimato qualcosa che in realtà è semplice?

4. ANTI-PATTERN
   - Stai replicando soluzioni generiche invece di rispondere al contesto specifico?
   - Stai ignorando vincoli che l'utente ha dichiarato?
   - Stai aggiungendo complessità inutile?

5. DOMANDE APERTE
   - Ci sono sezioni del file marcate con [?] — le hai risolte?
   - Ci sono decisioni critiche che hai rimandato senza dirlo?

Dopo questa analisi:
- Se trovi errori o incoerenze → CORREGGI il file e indica cosa hai cambiato
- Se il file regge → scrivi [RITROSO-VERIFIED] e spiega brevemente perché

Ricorda: tutto quello che hai detto deve essere coerente con il prompt originale.
Analizzalo da più punti di vista. Non è sufficiente che "abbia senso in astratto" —
deve avere senso per QUESTO progetto, per QUESTO utente, in QUESTO momento.
```

---

## Note di Utilizzo

- `[PROMPT_UTENTE_ORIGINALE]` viene sostituito con il contenuto del file `.ritroso_prompt_cache.tmp`
- Questo prompt NON è opzionale — fa parte del flusso obbligatorio di Ritroso.md
- Il modello non può saltare questo step anche se "è sicuro" del proprio output
- Le correzioni apportate vengono loggate nel file finale con prefisso `[CORRECTION]`
