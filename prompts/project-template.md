# Template File .md Temporaneo di Progetto — Ritroso.md

> Questo è il template strutturale che il modello usa per generare
> il file `.md` temporaneo in `new-ideas/`.
> Il modello può adattare ogni sezione al contesto — ma non può eliminare
> le sezioni marcate come [REQUIRED].

---

```markdown
# [NOME_PROGETTO] — Project Temp File
> Generato da: Ritroso.md skill
> Stato: [DRAFT | RITROSO-VERIFIED]
> Dominio: [dev | design | business | time | mixed]
> Data generazione: [DATA]

---

## [REQUIRED] SCOPE
<!-- Cosa fa questo progetto. In modo diretto, senza giri di parole.
     Il modello può usare notazioni compresse o simboliche se lo preferisce.
     Es: input→process→output / USER:action→SYSTEM:response -->

[scope_content]

---

## [REQUIRED] MAPPA_INIZIALE
<!-- Prima di tutto: struttura ad alto livello.
     Dipendenze, flusso principale, componenti chiave.
     Formato libero ma deve essere leggibile dal modello stesso.
     Usa ASCII, frecce, token simbolici — l'obiettivo è velocità di comprensione. -->

[mappa_content]

---

## [REQUIRED] RISORSE
<!-- Output del context-mapper: cosa esiste già nel workspace -->

RISORS_TROVATE:
[lista_risorse]

GAPS:
[cosa_manca]

---

## [REQUIRED] VINCOLI
<!-- Limiti tecnici, economici, temporali, architetturali -->

TECNICI:
[vincoli_tecnici]

TEMPO:
[vincoli_tempo]

RISORS:
[vincoli_risorse]

---

## [REQUIRED] ANTI_PATTERNS
<!-- Cose da NON fare in questo progetto -->
<!-- Derivate dal prompt utente + ragionamento interno -->

[anti_patterns]

---

## CONTEXT_DOMAIN_SPECIFIC
<!-- Sezione adattiva al dominio. Il modello sceglie il formato appropriato:

     SE dominio=DESIGN:
       - Componenti UI, stati, interazioni, accessibilità
       - Layout wireframe testuale
     
     SE dominio=SVILUPPO:
       - Architettura tecnica, endpoint API, struttura dati
       - Stack, dipendenze, pattern scelti
     
     SE dominio=BUSINESS/VENDITA:
       - Funnel, KPI, metriche di successo
       - Rischi economici, costi, ROI stimato
     
     SE dominio=TEMPO/PLANNING:
       - Milestone, dipendenze tra task, critical path
       - Buffer, rischi di slittamento
     
     SE dominio=MIXED:
       - Combina le sezioni pertinenti
-->

[domain_specific_content]

---

## [REQUIRED] DOMANDE_APERTE
<!-- Tutto quello che il modello non sa con certezza -->
<!-- Ogni item marcato con [?] attende risposta -->

[domande_aperte]

---

## LOG_VERIFICHE
<!-- Compilato durante il prompt negativo -->
<!-- Formato: [CORRECTION] descrizione | [VERIFIED] motivazione -->

[log_verifiche]
```

---

## Istruzioni al Modello per la Compilazione

1. **Non saltare sezioni [REQUIRED]** — anche se sembrano vuote, scrivi `N/A + motivazione`
2. **La MAPPA_INIZIALE viene prima di tutto** — anche prima di capire i dettagli
3. **Usa il formato più veloce per te** — stringhe compresse, simboli, notazioni interne
4. **Ogni `[?]` è un debito** — va risolto nel prompt negativo o segnalato come OPEN
5. **Il file è per il modello, non per l'utente** — ottimizza per comprensione interna
