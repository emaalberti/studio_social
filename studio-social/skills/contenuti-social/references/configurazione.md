# Configurazione fonte grafiche

## Fonte attiva

FONTE: onedrive        <!-- valori possibili: onedrive | confluence | entrambe -->

## OneDrive / SharePoint — organizzazione per progetti

Le grafiche non sono in un'unica cartella: ogni progetto (app) ha il suo spazio. Può essere una cartella nella sezione **Condivisi / Shared** di OneDrive oppure un **sito SharePoint** dedicato (es. `devnut.sharepoint.com/sites/turno`).

### Come trovare la cartella giusta

1. **Progetto**: se l'utente non lo indica, chiedigli quale progetto usare (pulsanti, max 4 opzioni + "Altro"), partendo dai progetti noti qui sotto.
2. **Se il progetto è tra i progetti noti**, usa i percorsi indicati lì e salta la ricerca.
3. **Altrimenti** cerca la cartella per nome del progetto (ricerca cartelle del connettore, non solo la radice del drive personale) e, al suo interno, una sottocartella chiamata in quest'ordine:
   - `<Progetto>-Design` (es. `Turno-Design`)
   - `Design-system` / `Design system` / `DesignSystem`
   - qualunque cartella il cui nome contenga `design` (senza distinguere maiuscole/minuscole)
4. Se ne trovi più di una, preferisci quella modificata più di recente e cita entrambe nella proposta. Se non ne trovi nessuna, cerca cartelle come `Marketing`, `Screenshot`, `assets`, `Loghi`; se non basta, mostra all'utente le sottocartelle del progetto e chiedi quale contiene le grafiche.

### Progetti noti

**Turno** — piattaforma per creare e gestire tornei sportivi amatoriali (calcio, padel, basket, volley).
- Sito SharePoint `turno`, raccolta "Documenti condivisi". Non esiste una cartella design né un brand book.
- Screenshot: `Marketing/Screenshot/Screenshot area admin/` (`risultati.png`, `classifiche.png`, `classifiche elim diretta.png`, `fasi.png`, `programma.png`; vista admin desktop).
- Illustrazioni sport: `General/assets/formato originale/` e `General/assets/formato webp/` (sottocartelle per sport).
- `General/Loghi/[A]…[Z]` contiene i loghi delle **squadre**, non quello di Turno. Un file del logo Turno non c'è: se serve, usa il logo visibile negli screenshot (in alto a sinistra, su sfondo navy) e avvisa l'utente che è a bassa risoluzione.
- Pitch deck: `Marketing/pitch/Turno Sport Pitch Deck_v1.pdf`. È marcato "Riservato": vedi la regola in `brand.md`.
- Palette ricavata dagli screenshot: navy #0D1421 (sidebar), blu primario #0F69EC (logo), blu pulsanti #00429C, sfondo #EFF1F2. Font: Inter (quello dell'app non è identificato con certezza).

## Confluence (fonte secondaria)

Le grafiche ufficiali stanno su OneDrive/SharePoint. Confluence serve solo a integrare: linee guida di design, brand book o allegati di un progetto.

- Sito: `devnut.atlassian.net`
- Spazio principale: `DEVNUT`
- Non c'è una pagina fissa: dopo aver scelto il progetto, cerca le pagine il cui titolo o testo contiene il nome del progetto insieme a "design", "UX/UI", "brand" o "logo" (es. CQL `space = DEVNUT AND text ~ "<Progetto>" AND (title ~ "design" OR title ~ "brand")`). Se trovi più pagine, usa la più recente e citala nella proposta; se non ne trovi, prosegui solo con OneDrive.

## Cosa puoi usare dal connettore Microsoft 365

Il connettore **non scarica file**: `read_resource` restituisce testo (HTML, Markdown, JSON, XML, txt, PDF, Office) oppure un'immagine da guardare, e ogni risultato di ricerca ha `downloadUrl: null`. Quindi:

- **Utilizzabile nel post:** tutto ciò che arriva come testo. SVG scritti dentro un `.html` o un `.xml` (es. `styleguide.html`, `logo.svg.xml`), componenti HTML e CSS, token e testi.
- **Solo riferimento visivo:** PNG, JPG e WebP. Puoi guardarli per copiare layout e colori, ma non puoi inserirli nell'artifact.
- **Non leggibile:** file `.svg` separati, font (`.woff`, `.ttf`), `.fig`.

Per usare uno screenshot o una foto veri serve il file: chiedi all'utente di caricarlo nella chat oppure, in Claude Code, leggilo dalla cartella OneDrive sincronizzata in locale (`~/Library/CloudStorage/OneDrive-…`).

## Struttura standard della cartella design

Le linee guida complete per chi prepara la cartella sono nel documento "Linee guida cartella design per i contenuti social". La struttura attesa è:

```
<Progetto>/
  design-system/            (o <Progetto>-Design)
    README.md               prodotto, pubblici, tono, regole del logo, cose da evitare, indice della styleguide
    tokens.json             colori HEX, font (con alternativa Google Fonts), raggi
    styleguide.html         TUTTE le grafiche come SVG inline: logo, illustrazioni, icone, componenti, schermate chiave
    svg-testo/              copie dei singoli SVG in .svg.xml (facoltativo)
    sorgenti/               originali per i designer (.svg, .ai, .fig, .png): non usarli
  marketing/
    testi-approvati.md      claim, descrizioni, CTA con link, hashtag, dati verificati
    post/                   post già pubblicati, riferimento di stile
    screenshot/             PNG di riferimento, nomi parlanti
```

Ordine di lettura: `README.md` → `tokens.json` → `styleguide.html` (gli elementi hanno id parlanti, es. `logo-orizzontale-chiaro`) → `marketing/testi-approvati.md`. Il README del progetto ha la precedenza su `brand.md`.

Se la cartella non segue questa struttura, cerca comunque file di testo che contengano SVG (`.html`, `.xml`) e cartelle con nomi come `loghi`, `illustrazioni`, `icone`, `colori`, `font`. Se nella cartella design c'è un brand book (PDF, documento), leggilo: per quel progetto ha la precedenza su `brand.md`.
