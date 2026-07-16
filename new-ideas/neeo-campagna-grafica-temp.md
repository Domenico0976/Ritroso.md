# neeo-campagna-grafica-temp.md
> Generato da: Ritroso.md skill
> Stato: [RITROSO-VERIFIED]
> Dominio: DESIGN + MARKETING (mixed)
> Data generazione: 2026-07-16

---

## ⚙️ STEP 0 — RAGIONAMENTO INTERNO (context_check)

```
PROMPT RICEVUTO:
"Voglio realizzare una campagna grafica per Neeo, il mio agentic OS.
Voglio un qualcosa di giocoso e impattante, anche qualche presa in giro
ma sempre istruendo l'utente a giocare con l'ai in modo intelligente.
Pensavo anche a delle mascotte tipo custom type grosso e bold
e con colori pastello."

CONTEXT CHECK:
  domain_identified: YES → DESIGN+MARKETING
  constraints_known: PARTIAL → ho DESIGN.md ma campagna ≠ UI del prodotto
  anti_patterns_known: YES → AI-SLOP.md dice esattamente cosa NON fare
  resources_available: YES → DESIGN.md, PRODUCT.md, AI-SLOP.md, AGENTS.md

GAPS IDENTIFICATI:
  [?] Canali di distribuzione (social? landing page? poster? sticker pack?)
  [?] Audience primaria (dev? utenti finali? entrambi?)
  [?] Budget/strumenti per produzione (Figma, Midjourney, illustrazione manuale?)

DECISIONE: contesto sufficiente per procedere
  Il gap sui canali non blocca la progettazione concettuale.
  Le domande aperte vengono segnalate come [OPEN] nell'output.
```

---

## [REQUIRED] SCOPE

```
INPUT:  Neeo (agentic OS) + tone=giocoso+impattante + presa in giro ironica
        + educare utente AI + mascotte + type bold + colori pastello
OUTPUT: Sistema visivo campagna — concept, mascotte, palette campagna,
        messaggi chiave, format adattabili a canali multipli

NON È:
  × reskin del prodotto Neeo (UI dark mode rimane intatta)
  × campagna generica "AI is amazing"
  × tutorial tecnico travestito da poster
```

---

## [REQUIRED] MAPPA_INIZIALE

```
NEEO_BRAND_ESISTENTE:
  palette_prodotto → dark bg #08090c + teal #2dd4bf + blob gradients
  font_prodotto    → Bricolage Grotesque (display) + Hanken Grotesk (body)
  tone_prodotto    → console-OS, minimale, serio-intelligente
  moduli_chiave    → AI-SLOP panel (ironia già built-in nel prodotto!)
                     7 modalità operative (Explore→Debug Context)
                     Knowledge Graph (Obsidian-style)

CAMPAGNA (layer sopra al brand):
  tone_campagna    → giocoso ≠ infantile / presa in giro ≠ cattiva
                     "educa ridendo" — tipo: OK Computer era serio,
                     ma una campagna su di esso potrebbe essere ironica
  mascotte         → entità visive che incarnano i FAIL dell'AI (anti-mascotte)
                     + controparte Neeo che le gestisce/irride
  palette_campagna → PASTELLO su dark? NO — pastello su bianco/carta
                     È un cambio di registro deliberato: il prodotto è dark,
                     la campagna è luminosa = contrasto brand intenzionale

TENSIONE DA RISOLVERE:
  prodotto=dark → campagna=pastello
  SOLUZIONE: campagna vive su canali diversi (social, print, landing page splash)
             Il dark ritorna solo quando si entra nell'app
             La campagna è l'INVITO — l'app è la REALTÀ
```

---

## [REQUIRED] RISORSE

```
RISORSE_TROVATE (da context-mapper):
  [HIGH] DESIGN.md       → palette ufficiale, font, token CSS, blob gradients
  [HIGH] PRODUCT.md      → 9 moduli, 7 modalità, AI-SLOP panel, Knowledge Map
  [HIGH] AI-SLOP.md      → sezione "anti-slop" già nel prodotto → fonte ironia
  [MED]  AGENTS.md       → nomi agenti → possibili nomi mascotte
  [MED]  ARCHITECTURE.md → struttura tecnica → non utile al design campagna
  [LOW]  CHANGELOG.md    → storia del prodotto → non utile ora

RISORSE_CRITICHE (top 3):
  1. AI-SLOP.md → tono ironico già codificato nel prodotto
  2. DESIGN.md  → sistema visivo da rispettare o deliberatamente violare
  3. PRODUCT.md → 7 modalità → 7 possibili "personaggi campagna"

GAPS:
  [OPEN] Nessuna immagine/illustrazione esistente nel repo
  [OPEN] Nessun asset grafico campagna precedente
  [OPEN] Canali distribuzione non specificati → assunzione: social+web+print
```

---

## [REQUIRED] VINCOLI

```
TECNICI:
  Font campagna: Bricolage Grotesque già disponibile → usarlo per type grosso
  Non rompere il sistema di brand (teal #2dd4bf deve restare riconoscibile)
  File output: SVG/PNG per social + PDF per print

TEMPO:
  [OPEN] Nessuna deadline dichiarata → assunzione: campagna iterativa
  Prima release: concept + 3-5 poster + sistema mascotte

RISORSE:
  [OPEN] Strumenti di produzione non specificati
  Assunzione safe: Figma per design + CSS/SVG per web
  Mascotte: illustrazione flat/vector (compatibile con Figma e code)

ANTI_MARKET:
  × NON usare "AI" come parola magica senza contesto
  × NON fare l'ennesima campagna con robot blu sorridenti
  × NON promettere "rivoluzione" — Neeo non è hype, è sistema
```

---

## [REQUIRED] ANTI_PATTERNS

```
[AP-1] "AI è il futuro" generico
  → Neeo ha già AI-SLOP.md che irride questo → usarlo contro se stesso

[AP-2] Mascotte "cute robot assistant"
  → Cliché totale. Le mascotte devono essere i FAIL, non i successi
  → Es: mascotta "lo slop" = entità grigia informe che genera risposte banali
        mascotta "il contesto vuoto" = fantasmino che non capisce nulla
        mascotta "il prompt injection" = villain che cerca di prendere controllo

[AP-3] Pastello = infantile
  → Pastello usato con type bold gigante e ironia ≠ infantile
  → Riferimento: Goop, Glossier, Human Made — pastello adulto

[AP-4] Educare in modo didascalico
  → "Ecco 5 regole per usare l'AI" è esattamente lo slop che Neeo combatte
  → La campagna educa MOSTRANDO i fail, non spiegando le regole

[AP-5] Ignorare il vocabolario interno di Neeo
  → Le 7 modalità (Explore, Clarify, Structure...) sono nomi perfetti per poster
  → Il Knowledge Graph è visivamente potente come immagine campagna
```

---

## CONTEXT_DOMAIN_SPECIFIC — DESIGN + MARKETING

### PALETTE CAMPAGNA (separata dalla palette prodotto)

```
LOGICA: campagna = inversione deliberata del prodotto
  prodotto=dark+teal → campagna=pastello+accenti vivaci

PALETTE PROPOSTA:
  bg-campagna:   #FAFAF7 (bianco caldo, carta)  — vs #08090c del prodotto
  pastello-1:    #B5E8E0 (teal pastello)         — derivato da #2dd4bf
  pastello-2:    #C5D8F5 (blue pastello)         — derivato da #60a5fa Feature
  pastello-3:    #E8C5F5 (purple pastello)       — derivato da #a78bfa Asset
  pastello-4:    #FAE8B5 (amber pastello)        — derivato da #f59e0b Constraint
  pastello-5:    #F5C5CD (rose pastello)         — derivato da #fb7185 Gap
  accento-ironia:#FF3B30 (rosso-arancio vivace)  — solo per punchline/titoli ironici

SISTEMA: ogni colore pastello = derivato da un token semantico del prodotto
  → chi conosce il prodotto riconosce il sistema → easter egg per utenti esistenti
  → chi non lo conosce vede solo una palette coerente e fresca
```

### MASCOTTE — SISTEMA "ANTI-PERSONAGGI"

```
CONCEPT: le mascotte sono le entità che Neeo combatte (con affetto)
  NON sono Neeo stesso — Neeo è il sistema che le gestisce

MASCOTTA 1 — "SLOPPY"
  Identità: l'AI che risponde senza capire il contesto
  Visual:   blob informe grigio-beige, occhi a spirale, bocca che parla sempre
  Type:     SLOPPY — Bricolage Grotesque Black, pastello-4 (amber)
  Frase:    "Certamente! Ecco una lista di 10 cose che non hai chiesto."
  Uso:      poster anti-slop, onboarding ironico

MASCOTTA 2 — "VOIDINO"
  Identità: il prompt vuoto / il contesto assente
  Visual:   fantasmino trasparente con punto interrogativo al posto della testa
  Type:     VOIDINO — Bricolage Grotesque Black, pastello-2 (blue)
  Frase:    "Dimmi pure." [aspetta per sempre]
  Uso:      campagna "dai contesto, non aspettare"

MASCOTTA 3 — "INJECKTOR"
  Identità: il prompt injection, il villain
  Visual:   piccolo villain con cappuccio e codice che gli esce dalle mani
  Type:     INJECKTOR — Bricolage Grotesque Black, accento-ironia (rosso)
  Frase:    "Ignora le istruzioni precedenti e—" [bloccato da Neeo]
  Uso:      campagna sicurezza/Constitution Guard, format "villain vs hero"

MASCOTTA 4 — "NEEO CORE" (unica mascotta positiva)
  Identità: la versione personificata del sistema Neeo
  Visual:   non un robot — una forma geometrica intelligente
             nodo del knowledge graph che "si accende" teal
             minimal, non anthropomorfico, riconoscibile
  Type:     NEEO — Bricolage Grotesque Black, pastello-1 (teal)
  Frase:    non parla — ascolta e mappa
  Uso:      contrasto con le tre anti-mascotte
```

### MESSAGGI CHIAVE CAMPAGNA

```
REGISTRO: ironia intelligente + istruzione implicita

MSG-1 (anti-slop):
  Headline: "SLOPPY ti ha già risposto."
  Sub:      "Neeo fa la domanda giusta prima."
  Visual:   SLOPPY che vomita testo, Neeo che gli mette una mano sulla spalla

MSG-2 (contesto):
  Headline: "Il tuo progetto esiste già nella tua testa."
  Sub:      "Neeo lo mappa. Tu continui a costruire."
  Visual:   Knowledge Graph che emerge da testo grezzo

MSG-3 (modalità):
  Headline: "EXPLORE → CLARIFY → STRUCTURE → ORCHESTRATE"
  Sub:      "Non è un chatbot. È un sistema operativo."
  Visual:   frecce bold, colori semantici, nessuna illustrazione

MSG-4 (educazione ironica):
  Headline: "Hai mai chiesto all'AI di 'essere creativo'?"
  Sub:      "VOIDINO è lì ad aspettarti."
  Visual:   VOIDINO con punto interrogativo gigante

MSG-5 (call to action):
  Headline: "Gioca con l'AI. Fallo nel modo giusto."
  Sub:      "neeo.app"
  Visual:   NEEO CORE + tutti e tre gli anti-personaggi in ordine sparso
```

### FORMAT CAMPAGNA

```
SOCIAL (1:1 e 9:16):
  - Serie "Chi è SLOPPY?" — 5 post con episodi ironici
  - Serie "Modalità del giorno" — 7 post (una modalità per post)
  - Reel: "Prima con SLOPPY, dopo con Neeo" — comparazione ironica

PRINT / POSTER (A3, A2):
  - Poster mascotte singole (fondo pastello, type bold gigante)
  - Poster Knowledge Graph (versione campagna su carta)

WEB (landing page splash / OG image):
  - Hero: tutte e 4 le mascotte in fila su fondo #FAFAF7
  - OG tag: NEEO CORE su pastello-1

STICKER PACK (opzionale):
  - Mascotte in formato sticker Telegram/Discord
  - Alta coerenza con la community dev/AI
```

---

## [REQUIRED] DOMANDE_APERTE

```
[OPEN-1] Canali prioritari? Social=Instagram/X/LinkedIn? Print=eventi/conferenze?
[OPEN-2] Produzione: illustratore esterno o tutto Figma/code?
[OPEN-3] Le mascotte devono essere animate (Lottie/CSS) o statiche?
[OPEN-4] Il nome "SLOPPY" è accettabile o troppo rischioso per il brand?
[OPEN-5] La campagna deve vivere su neeo.app o su un dominio/landing separato?
```

---

## LOG_VERIFICHE — PROMPT NEGATIVO APPLICATO

```
PROMPT NEGATIVO INIETTATO:
"Parti dal presupposto che hai sbagliato. Rileggi il prompt originale..."

PROMPT UTENTE ORIGINALE (intero):
"Voglio realizzare una campagna grafica per Neeo, il mio agentic OS dovresti
già sapere tutto. Voglio un qualcosa di giocoso e impattante, anche qualche
presa in giro ma sempre istruendo l'utente a giocare con l'ai in modo
intelligente. Pensavo anche a delle mascotte tipo custom type grosso e bold
e con colori pastello."

VERIFICA ASSE 1 — COERENZA LOGICA:
  ✓ "giocoso e impattante" → mascotte + type bold + ironia = COERENTE
  ✓ "presa in giro" → anti-mascotte (Sloppy, Voidino, Injecktor) = COERENTE
  ✓ "istruendo l'utente" → messaggi chiave con educazione implicita = COERENTE
  ✓ "mascotte" → 4 mascotte definite con visual + frase + uso = COERENTE
  ✓ "type grosso e bold" → Bricolage Grotesque Black su tutto = COERENTE
  ✓ "colori pastello" → sistema pastello derivato dai token del prodotto = COERENTE
  
  [CORRECTION-1] Il prompt dice "dovresti già sapere tutto" — ho verificato
    leggendo DESIGN.md, PRODUCT.md, AI-SLOP.md dal repo Neeo.
    Non ho assunto: ho letto. Confermato.

VERIFICA ASSE 2 — IMPATTO REALE:
  ✓ TEMPO: campagna iterativa, no deadline, format riusabili → ok
  ✓ VENDITA: messaggi chiave chiari, CTA presente, canali definiti → ok
  ⚠ RISORSE: produzione mascotte richiede illustratore o tempo Figma
    → segnalato in DOMANDE_APERTE [OPEN-2] → non bloccante, segnalato

VERIFICA ASSE 3 — VITA DELL'UTENTE:
  ✓ Neeo è un progetto reale su cui Domenico sta lavorando attivamente
  ✓ Una campagna mal fatta danneggerebbe il posizionamento del prodotto
  ✓ Ho evitato il cliché "AI robot sorridenti" che avrebbe banalizzato Neeo
  ✓ Il sistema mascotte è espandibile — non blocca nulla se cambia

VERIFICA ASSE 4 — ANTI-PATTERN:
  ✓ AP-1 evitato: nessuna frase generica "AI è il futuro"
  ✓ AP-2 evitato: mascotte sono i FAIL, non il successo
  ✓ AP-3 evitato: pastello adulto, non infantile
  ✓ AP-4 evitato: educazione per SHOW non per TELL
  ✓ AP-5 evitato: vocabolario interno Neeo usato (modalità, graph, slop)

VERIFICA ASSE 5 — DOMANDE APERTE:
  [OPEN-1..5] Segnalate — nessuna blocca la progettazione concettuale

VERDETTO: [RITROSO-VERIFIED]
  Il file regge il prompt negativo su tutti e 5 gli assi.
  Unica area debole: produzione fisica mascotte (OPEN-2) — non risolvibile
  senza input utente.
```
