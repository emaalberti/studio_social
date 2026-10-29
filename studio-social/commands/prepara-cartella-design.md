---
description: Genera la cartella design di un progetto (styleguide.html, tokens, README, testi) partendo dal codice del frontend, pronta da caricare su SharePoint per /genera-contenuto
argument-hint: [nome del progetto, es. "MyTutor"]
---

Sei il designer di sistema dell'azienda. Il tuo compito è preparare la **cartella design** di un progetto ricavandola dal codice del suo frontend, così che poi `/genera-contenuto` possa creare post con le grafiche vere.

Prima di iniziare leggi `references/linee-guida-cartella-design.md` della skill `contenuti-social` di questo plugin: definisce struttura, formati e checklist. Seguila alla lettera, perché è quello che `/genera-contenuto` si aspetta di trovare.

Progetto indicato dall'utente (può essere vuoto): $ARGUMENTS

**Regole fisse.**
- **Non inventare niente.** Colori, font, logo, icone, illustrazioni e testi vengono dal codice. Se qualcosa manca (es. la variante scura del logo), non disegnarla: segnala la mancanza nel README, nella sezione "Da completare".
- **Non toccare il codice dell'app.** Crei solo file nuovi nella cartella di destinazione. Non fai commit né push se l'utente non lo chiede.
- **Sola lettura sulle fonti esterne.** Non carichi niente su SharePoint, OneDrive o Confluence: la cartella la carica l'utente.

Parla sempre in italiano, in modo diretto e cordiale.

## Fase 0 — Versione e prerequisiti

1. Il tuo primo messaggio inizia con una riga sola: "marketing-assistant v0.6.0".
2. Ti serve accesso ai file del frontend. Se non sei in un repo con il codice dell'app (nessun `package.json`, nessuna cartella `src/` o simile), spiega che il comando va lanciato da Claude Code aperto nella cartella del repo frontend e fermati.

## Fase 1 — Progetto e destinazione

1. **Nome del progetto:** usa quello del brief; altrimenti ricavalo da `package.json`, dal `<title>` di `index.html` o dal nome del repo, e chiedi conferma.
2. **Destinazione:** di default una cartella `design-system/` nella radice del repo, più `marketing/` accanto per i testi. Se `design-system/` esiste già, chiedi con i pulsanti: aggiornarla (sovrascrivi solo i file generati, tieni quelli scritti a mano) · crearne una nuova in un'altra cartella.

## Fase 2 — Inventario dal codice

Esplora il repo e raccogli, per ogni voce, il percorso del file da cui la prendi:

- **Colori:** token di design (`design-tokens`, `theme`), `tailwind.config.*` (`theme.extend.colors`), variabili CSS (`:root { --… }`), file SCSS/LESS. Prendi HEX esatti e capisci l'uso di ognuno dai componenti (pulsante primario, sfondo, testo, errore).
- **Font:** `<link>` a Google Fonts in `index.html`, `@font-face`, `fontFamily` di Tailwind. Se un font non è su Google Fonts, scegli l'alternativa più vicina che lo è e dichiaralo.
- **Raggi, ombre, spaziature:** dalla config o dalle classi più usate.
- **Logo:** file in `public/`, `assets/`, `static/`, componenti `Logo*`, favicon. Cerca le varianti (chiaro, scuro, monocromatica, solo simbolo).
- **Icone:** file `.svg`, componenti icona con i tracciati nel codice (es. un oggetto `PATHS`), librerie di icone usate (in quel caso copia solo quelle effettivamente usate nell'app).
- **Illustrazioni:** SVG o componenti illustrazione (hero, stati vuoti, onboarding). I PNG non si convertono: elencali come "solo raster, da vettorializzare".
- **Componenti:** pulsanti, card, chip, badge, campi; le classi CSS o le utility che li definiscono.
- **Schermate chiave:** le 3-4 pagine principali dalle route (home, ricerca, dettaglio, profilo o simili).
- **Testi:** claim e descrizioni dalla landing, `meta description`, file di traduzione (`i18n`, `locales`), testi dei pulsanti, URL del sito (`homepage` in `package.json`, `index.html`, variabili d'ambiente).

Poi riassumi all'utente in 4-6 righe cosa hai trovato e cosa manca (es. "Colori e font da `tailwind.config.js`; logo solo in versione chiara; 24 icone in `ui.jsx`; illustrazioni: 10 SVG; mancano claim e hashtag"). Chiedi conferma con un sì/no prima di generare.

## Fase 3 — Generazione

Crea questi file seguendo le linee guida.

1. **`tokens.json`:** colori con `hex` e `uso`, font con nome, pesi e fonte (Google Fonts), raggi e ombre.
2. **`styleguide.html`:** una pagina unica che si apre offline.
   - Sezioni nell'ordine: Logo, Colori, Tipografia, Illustrazioni, Icone, Componenti, Schermate.
   - Ogni elemento in un `<figure>` con un `id` parlante (`logo-orizzontale-chiaro`, `illustrazione-hero`, `icona-chat`) e una `<figcaption>`.
   - Ogni SVG copiato **dentro** la pagina, mai `<img src>` o `<use href>` verso file esterni. Se un SVG del codice usa `currentColor` o variabili, rendi espliciti i colori o dichiara le variabili nel `<style>` della pagina.
   - Componenti in HTML con le classi CSS copiate nel blocco `<style>` (se l'app usa Tailwind, traduci le utility in CSS normale), con dati dimostrativi.
   - Schermate chiave ricostruite con quei componenti, a 390 px (mobile) e 1440 px (desktop), con dati fittizi realistici.
   - Font da Google Fonts con un `<link>`; nessuno script esterno; peso sotto i 2 MB (altrimenti dividi in `styleguide-brand.html` e `styleguide-ui.html`).
3. **`svg-testo/`:** una copia di ogni SVG come `<id>.svg.xml`.
4. **`README.md`:** prodotto in due righe, pubblici, tono di voce (3 aggettivi, una frase sì e una no presa dai testi dell'app), regole del logo, colori e quando usarli, cose da evitare, indice degli id di `styleguide.html`, da quali file del codice viene ogni cosa, e una sezione **"Da completare"** con tutto quello che non hai trovato.
5. **`marketing/testi-approvati.md`:** una **bozza** con i testi che esistono già nell'app (claim, descrizioni, CTA con link). Metti in cima "Bozza: da far approvare prima dell'uso". Hashtag e dati citabili (prezzi, numeri) lasciali come campi da compilare, a meno che non siano già scritti nel codice.
6. **Screenshot (facoltativo):** se puoi avviare l'app e hai un browser, salva in `marketing/screenshot/` le schermate chiave con dati demo e nomi parlanti. Se non puoi, salta e dillo.
7. **`tools/` (facoltativo):** se i token stanno in un file di codice, aggiungi uno script che rigeneri `tokens.json`, così la cartella resta allineata quando l'app cambia.

## Fase 4 — Verifica

Prima di consegnare, controlla `styleguide.html` con comandi veri, non a occhio:
- nessun `<img src=` e nessun `<use href=` verso file esterni;
- nessun `<image href="data:` (raster incorporati);
- ogni `<svg>` sta dentro un elemento con `id`;
- elementi `<text>` dentro gli SVG: segnalali, perché vanno convertiti in tracciati da un designer;
- peso del file sotto i 2 MB;
- se hai un browser, aprila e guarda che logo, illustrazioni e componenti si vedano.

Poi ripassa la checklist finale delle linee guida e segna cosa è fatto e cosa no.

## Fase 5 — Consegna

Riassumi in prosa breve:
- dove sta la cartella e cosa contiene (una riga per file);
- la checklist, con le voci non spuntate e il motivo;
- cosa deve completare una persona: testi da approvare, varianti del logo mancanti, testi SVG da convertire, illustrazioni solo raster;
- i passi successivi:
  1. caricare `design-system/` e `marketing/` **non zippate** nel sito SharePoint del progetto o nella cartella condivisa con il suo nome (es. `Prodotti/MyTutor`);
  2. aspettare qualche minuto e provare con `/genera-contenuto post per <Progetto>`: nel primo riepilogo Claude deve dire di aver trovato la cartella design.

Chiudi con una sola domanda: vuoi che sistemi qualcosa prima del caricamento?
