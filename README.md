# Ritroso.md

> Sistema di skill YAML per ragionamento contestuale LLM con auto-verifica retroattiva

## Cos'è

**Ritroso.md** è un framework di skill per modelli LLM che forza il modello a:
1. Raccogliere contesto completo prima di agire
2. Generare un file `.md` temporaneo di progetto come "mappa mentale leggibile dal LLM"
3. Sottoporsi a un **prompt negativo di auto-verifica** — il modello viene attaccato sul suo stesso output e deve difendersi con coerenza

Il nome "Ritroso" indica il movimento: **il modello va avanti, poi torna indietro a verificare**.

---

## Struttura

```
Ritroso.md/
├── skills/
│   ├── ritroso.yaml          # Skill principale — flusso completo
│   └── context-mapper.yaml   # Skill ausiliaria — mappatura risorse progetto
├── prompts/
│   ├── negative-verification.md  # Prompt negativo di auto-verifica
│   └── project-template.md       # Template .md temporaneo di progetto
├── new-ideas/                # Cartella output dei file .md generati
│   └── .gitkeep
└── docs/
    └── how-it-works.md       # Documentazione tecnica del flusso
```

---

## Flusso rapido

```
[PROMPT UTENTE]
      ↓
[CONTEXT CHECK] → manca contesto? → [DOMANDA ESPANSIONE]
      ↓
[CONTEXT MAPPER] → scansiona risorse (md, code, img, pdf...)
      ↓
[GENERA project-temp.md] → in new-ideas/
      ↓
[PROMPT NEGATIVO] → "hai sbagliato tutto, dimostra che hai senso"
      ↓
[OUTPUT FINALE VALIDATO]
```

---

## Come usare

1. Carica la skill `ritroso.yaml` nel tuo sistema di skill (es. GitHub Copilot, OpenDevin, Continue, o custom agent)
2. Fornisci un prompt di progetto
3. Il sistema ti chiederà il contesto mancante se necessario
4. Troverai il file `.md` generato in `new-ideas/`
5. Il file verrà auto-verificato con il prompt negativo

---

## Filosofia

> *"Il modello deve avere torto prima di avere ragione."*

Il prompt negativo non è distruttivo — è il meccanismo che trasforma un output mediocre in uno coerente. Il LLM viene forzato a confrontare il suo lavoro con il prompt originale da **angoli ostili** (vendita, tempo, risorse, errori logici, contraddizioni).
