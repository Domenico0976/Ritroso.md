# Intestazione Standard File Set — Ritroso.md v2

> Questo file definisce l'intestazione standard che ogni file del set
> deve avere in cima. Il contenuto specifico di ciascun file è nei
> template individuali in `prompts/file-set-templates/`.

---

## Intestazione Standard (obbligatoria per ogni file)

```markdown
# {NUMERO}_{NOME}.md — {project_name}
> Generato da: Ritroso.md v2 skill
> Dominio: {domain_slug}
> Stato: [DRAFT | RITROSO-VERIFIED]
> Data: {data_generazione}
> Cartella: new-ideas/{domain_slug}/{project_name_slug}/
> Set: [[00_INDEX]] [[01_GOAL]] [[02_PRODUCT]] [[03_NEXT_STEPS]]
>      [[04_ELEMENTS]] [[05_COMPONENTS]] [[06_PRICE]] [[07_BUDGET]]
>      [[08_LIMITS]] [[09_AGENTS]] [[10_ERROR]] [[11_INTERPOLATION]] [[12_ASKED]]
---
```

## Regole di Compilazione Generali

1. **Wikilinks obbligatori** in ogni file — puntano agli altri 12 file del set
2. **N/A giustificato** — se una sezione non si applica: `N/A: [motivazione]`
3. **Formato libero** — il modello usa il formato più veloce per sé stesso
4. **Nessuna duplicazione** — se contenuto è già altrove: `→ vedi [[file]]`
5. **[OPEN]** per ogni assunzione non verificata o informazione mancante
6. **[CORRECTION]** per ogni correzione apportata durante il prompt negativo
