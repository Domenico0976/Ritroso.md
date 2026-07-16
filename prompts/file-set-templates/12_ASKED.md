# Template: 12_ASKED.md

> Il file più onesto del set.
> Risponde a: COSA NON SAPPIAMO ANCORA (domande, assunzioni, conflitti, rimandati).

---

```markdown
# 12_ASKED.md — {project_name}
{intestazione_standard}

## DOMANDE APERTE [OPEN]
<!-- Tutto ciò che il modello non sa con certezza -->
<!-- Formato: [OPEN-N] domanda → impatto se non risposta -->
{open_questions}

## ASSUNZIONI NON VERIFICATE
<!-- Cose assunte senza conferma esplicita dell'utente -->
<!-- Formato: [ASSUME-N] assunzione → fonte (prompt/inference/default) -->
{unverified_assumptions}

## DECISIONI RIMANDATE
<!-- Scelte che devono essere prese ma non ora -->
{deferred_decisions}

## CONFLITTI RILEVATI
<!-- Punti in cui due file del set si contraddicono -->
{conflicts}

## RICHIEDE INPUT UTENTE
<!-- Lista pulita di ciò che solo l'utente può rispondere -->
{needs_user_input}

## NOTE POST-VERIFICA
<!-- Compilato dal prompt negativo: cosa è rimasto irrisolto dopo la verifica -->
{post_verification_notes}

## DIPENDENZE
→ questo file è l'ultimo letto nel prompt negativo
→ ogni [OPEN] non risolto rimane come [OPEN] nel file finale
→ ogni conflitto trovato genera [CORRECTION] nel file corrispondente
```
