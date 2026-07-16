# Template: 10_ERROR.md

> Risponde a: COSA PUÒ ANDARE STORTO (errori, edge case, failure mode, recovery).

---

```markdown
# 10_ERROR.md — {project_name}
{intestazione_standard}

## FAILURE MODE CRITICI
<!-- Cosa può andare storto in modo bloccante -->
{critical_failures}

## EDGE CASE
<!-- Situazioni limite che il sistema deve gestire -->
{edge_cases}

## ERRORI UTENTE COMUNI
<!-- Cosa fa l'utente che potrebbe rompere il flusso -->
{user_errors}

## ERRORI DI SISTEMA
<!-- Race condition, timeout, stato inconsistente, OOM, drift agente -->
{system_errors}

## STRATEGIE DI RECOVERY
<!-- Come il sistema si riprende da ogni failure mode -->
{recovery_strategies}

## MONITORING
<!-- Come si rilevano gli errori in produzione -->
{monitoring}

## DIPENDENZE
→ vedi [[08_LIMITS]] per i vincoli che causano errori
→ vedi [[09_AGENTS]] per gli agenti che gestiscono gli errori
→ vedi [[12_ASKED]] per gli errori ancora da analizzare
```
