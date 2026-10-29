# Linee guida cartella design per i contenuti social

Come deve essere fatta la cartella design di un progetto perché `/genera-contenuto` possa usarla. Il comando `/crea_cartella_design` la genera seguendo queste regole a partire dalle repo frontend in locale; chi la prepara a mano usa la checklist in fondo.

## Perché servono

Claude può usare una grafica in un post solo se la legge come **testo**. Il connettore Microsoft 365 non scarica file: legge testo (HTML, Markdown, JSON, XML, PDF, Office) e mostra le immagini raster solo da guardare.

- **Funziona:** SVG scritti dentro un file HTML o XML, token di colore in JSON o Markdown, testi in Markdown o Word.
- **Non funziona:** file `.svg` separati (il connettore non li apre), PNG e JPG (Claude li vede ma non può inserirli), font `.woff`/`.ttf`, file `.fig`, archivi `.zip`.

Il caso MyTutor lo dimostra: il post è riuscito perché le illustrazioni erano dentro `styleguide.html`. Con BetonService si è bloccato perché c'erano solo un `.svg` separato e uno screenshot PNG.

## Struttura della cartella

Ogni progetto ha una cartella `design-system` (o `<Progetto>-Design`) con un file principale, `styleguide.html`, che contiene tutte le grafiche. Le altre cartelle sono di supporto.

```
<Progetto>/
  design-system/
    README.md            regole di brand, tono, cosa evitare
    tokens.json          colori, font, raggi, ombre
    styleguide.html      TUTTE le grafiche come SVG inline
    svg-testo/           copie dei singoli SVG in .svg.xml (facoltativo)
    sorgenti/            .svg, .ai, .fig, .png originali (per i designer)
  marketing/
    testi-approvati.md   claim, descrizioni, call to action
    post/                kit già pubblicati, per riferimento
    screenshot/          PNG delle schermate, solo come riferimento visivo
```

I nomi contano: il plugin cerca prima `design-system` o `<Progetto>-Design`, poi qualunque cartella con "design" nel nome. Usa nomi in minuscolo, senza spazi e senza date (`logo-orizzontale`, non `Logo_Def_v3 (1)`).

La cartella va caricata **non zippata** nel sito SharePoint del progetto (es. `devnut.sharepoint.com/sites/turno`) o nella cartella condivisa con il nome del progetto (es. `Prodotti/MyTutor`).

## Formati per tipo di contenuto

| Contenuto | Formato da usare | Da non usare da solo | Note |
|---|---|---|---|
| Logo (tutte le varianti) | SVG inline in `styleguide.html` | `.svg` separato, PNG | Versione su chiaro, su scuro e monocromatica |
| Illustrazioni | SVG inline in `styleguide.html` | PNG, WebP | Se esiste solo in raster, va vettorializzata |
| Icone | SVG inline in `styleguide.html` | Icon font, PNG | Una per riga, con nome visibile |
| Componenti UI (card, chip, pulsanti) | HTML + CSS in `styleguide.html` | Screenshot | Con dati dimostrativi realistici |
| Schermate chiave dell'app | HTML + CSS in `styleguide.html` | Solo PNG | 3-4 schermate, dati fittizi |
| Colori | `tokens.json` o tabella nel README | Solo nell'immagine | HEX esatti, con nome e uso |
| Font | Nome del font nel README | `.woff`, `.ttf` | Meglio Google Fonts; altrimenti un'alternativa simile |
| Testi approvati | `testi-approvati.md` o `.docx` | Testo dentro immagini | Claim, descrizioni, CTA |
| Foto e screenshot | PNG in `screenshot/`, solo come riferimento | Come unica fonte | Vedi "Screenshot e foto" |

## Come costruire styleguide.html

`styleguide.html` è una pagina unica che i designer aprono nel browser e che Claude legge come testo. Ogni grafica è scritta dentro la pagina, non collegata.

1. **Una sezione per categoria**, nell'ordine: Logo, Colori, Tipografia, Illustrazioni, Icone, Componenti, Schermate.
2. **Ogni elemento in un contenitore con un id parlante** e un titolo visibile:
   ```html
   <figure id="logo-orizzontale-chiaro">
     <svg viewBox="0 0 240 64" xmlns="http://www.w3.org/2000/svg">...</svg>
     <figcaption>Logo orizzontale · su sfondo chiaro</figcaption>
   </figure>
   ```
3. **SVG scritti nel file**, mai `<img src="logo.svg">` o `<use href="file.svg">`. Un link a un file esterno porta Claude allo stesso blocco del `.svg` separato.
4. **SVG puliti:** testi convertiti in tracciati, niente immagini raster incorporate (`<image href="data:...">`), colori in HEX o in variabili CSS dichiarate nella pagina. Da Figma, esporta con "Outline text" attivo.
5. **Componenti in HTML e CSS**, con le classi nel blocco `<style>` della pagina e dati dimostrativi (nome, prezzo, valutazione).
6. **Schermate chiave:** 3-4 schermate principali ricostruite con i componenti, a 390 px di larghezza per mobile e 1440 px per desktop. Usa dati fittizi già pronti, così non serve sfocare niente.
7. **Niente script esterni né font da file locali.** I font si caricano da Google Fonts con un `<link>`.
8. **Peso:** sotto i 2 MB. Oltre, dividila in `styleguide-brand.html` (logo, colori, illustrazioni) e `styleguide-ui.html` (componenti, schermate).

## README e token

Il README dice a Claude come usare le grafiche. Per quel progetto ha la precedenza sulle regole generali del plugin (`brand.md`).

`README.md` contiene:
- **Il prodotto in due righe:** cos'è, per chi, il sito ufficiale.
- **Pubblici:** chi sono (es. studenti, genitori, tutor) e cosa conta per ciascuno.
- **Tono di voce:** 3 aggettivi, con un esempio di frase sì e una no.
- **Regole del logo:** spazio di rispetto, dimensione minima, sfondi ammessi, cosa non fare.
- **Cose da evitare:** parole, colori o immagini vietati, dati riservati (es. pitch deck "Riservato").
- **Indice di `styleguide.html`:** l'elenco degli id con una riga di descrizione.

`tokens.json` contiene i valori esatti:

```json
{
  "colori": {
    "primario":  { "hex": "#0F69EC", "uso": "logo, link, accenti" },
    "sfondo":    { "hex": "#EFF1F2", "uso": "sfondo pagine" },
    "testo":     { "hex": "#0D1421", "uso": "titoli e testo" }
  },
  "font": {
    "titoli": { "nome": "Inter", "peso": 700, "fonte": "Google Fonts" },
    "testo":  { "nome": "Inter", "peso": 400, "fonte": "Google Fonts" }
  },
  "raggio": "12px"
}
```

Se il font del brand non è su Google Fonts, indica l'alternativa più vicina che lo è.

## Screenshot e foto

Screenshot e foto sono l'unico materiale che il connettore non può passare a Claude, qualunque formato si scelga. Vanno trattati così:

- **Di default si evitano:** le schermate ricostruite in `styleguide.html` coprono la maggior parte dei post e hanno già dati fittizi.
- **Nella cartella `screenshot/` restano come riferimento**, con nomi parlanti (`dashboard-fatture.png`, non `Screenshot 2026-06-08 alle 17.12.58.png`). Claude li guarda per capire l'interfaccia e copiarne layout e colori.
- **Quando un post ha bisogno della schermata vera**, Claude la chiede all'inizio, prima delle domande sul contenuto. Il file si carica nella chat (1-2 immagini) oppure, da Claude Code, si legge dalla cartella OneDrive sincronizzata sul Mac.
- **Mai dati reali di clienti negli screenshot di riferimento.** Se servono, usa un account demo, così non c'è niente da sfocare.
- **Foto (persone, ambienti):** stessa regola degli screenshot. Se il progetto ne usa spesso, conviene un set fisso da caricare quando serve.

## Testi approvati

Con i testi già approvati Claude non deve inventare claim, e i messaggi restano uguali su tutti i canali.

`marketing/testi-approvati.md` contiene:
- **Claim principale** e 2-3 varianti brevi.
- **Descrizione del prodotto** in tre lunghezze: una riga, 3 righe, un paragrafo.
- **Funzioni chiave**, ognuna con nome ufficiale e beneficio in una frase.
- **Call to action ammesse**, con il link di destinazione (es. "Scopri di più → mytutor-app.it").
- **Hashtag ufficiali** del progetto.
- **Dati che si possono citare** (prezzi, numeri, date), con la data in cui sono stati verificati.

In `marketing/post/` vanno i post già pubblicati, con caption e visual. Sono il riferimento per stile e tono.

## Checklist finale

La cartella è pronta quando ogni voce è spuntata.

- [ ] La cartella si chiama `design-system` o `<Progetto>-Design` ed è condivisa con chi crea i post
- [ ] `README.md` contiene prodotto, pubblici, tono, regole del logo e cose da evitare
- [ ] `tokens.json` contiene i colori in HEX e i font, con l'alternativa Google Fonts
- [ ] `styleguide.html` si apre nel browser e mostra tutte le grafiche
- [ ] Nessun `<img src>` o `<use href>` verso file esterni in `styleguide.html`
- [ ] Ogni SVG ha un id parlante e i testi convertiti in tracciati
- [ ] Ci sono le varianti del logo: su chiaro, su scuro, monocromatica
- [ ] Le 3-4 schermate chiave sono ricostruite in HTML con dati fittizi
- [ ] `testi-approvati.md` contiene claim, CTA con link, hashtag e dati verificati
- [ ] Gli screenshot di riferimento hanno nomi parlanti e nessun dato reale di clienti
- [ ] Prova: in una nuova chat lanci `/genera-contenuto` sul progetto e Claude trova tutto senza chiedere file
