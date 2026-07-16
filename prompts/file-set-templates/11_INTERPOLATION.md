# Template: 11_INTERPOLATION.md

> Risponde a: COME TUTTO SI CONNETTE (dipendenze, flussi dati, integrazioni, grafo).

---

```markdown
# 11_INTERPOLATION.md — {project_name}
{intestazione_standard}

## MAPPA CONNESSIONI
<!-- Come si collegano moduli/elementi/agenti tra loro -->
<!-- Formato: A → B (tipo_connessione) [direzione] -->
{connection_map}

## FLUSSI DI DATI
<!-- Come i dati si muovono: input → transform → output -->
{data_flows}

## INTEGRAZIONI ESTERNE
<!-- API terze parti, servizi, webhook, SDK -->
{external_integrations}

## DIPENDENZE CRITICHE
<!-- Se X si rompe, cosa smette di funzionare? -->
{critical_dependencies}

## EVENTI / TRIGGER
<!-- Cosa scatena cosa: user action → system reaction -->
{events_triggers}

## GRAFO SEMPLIFICATO
<!-- Versione testuale del knowledge graph del progetto -->
{simplified_graph}

## DIPENDENZE
→ vedi [[04_ELEMENTS]] per gli elementi collegati
→ vedi [[09_AGENTS]] per i flussi tra agenti
→ vedi [[10_ERROR]] per i failure mode nelle connessioni
```
