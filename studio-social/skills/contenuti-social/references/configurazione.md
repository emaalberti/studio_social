# Configurazione fonte grafiche

## Fonte attiva

FONTE: onedrive        <!-- valori possibili: onedrive | confluence | entrambe -->

## OneDrive / SharePoint — trovare il progetto da solo

Il plugin non contiene percorsi: ogni volta trova da solo la cartella design del progetto. Le grafiche di un progetto stanno in un **sito SharePoint** dedicato (es. `devnut.sharepoint.com/sites/turno`) oppure in una cartella condivisa su OneDrive.

**Non affidarti alla ricerca nel contenuto dei file** (`sharepoint_search`): i file caricati da poco possono non essere ancora indicizzati per ore. Usa la ricerca per nome di cartella e poi naviga leggendo gli elenchi delle cartelle con `read_resource`.

### 1. Trovare il progetto

- **Il brief nomina il progetto** (es. "post per Turno"): cerca le cartelle con quel nome (`sharepoint_folder_search`, nome = progetto). Nei risultati guarda `webUrl`: il progetto è il sito il cui percorso contiene `/sites/<progetto>/` (senza distinguere maiuscole e minuscole), oppure una cartella che si chiama esattamente come il progetto. Prendi il `driveId` di quel risultato: è la raccolta documenti del progetto.
- **Il brief non lo nomina:** cerca le cartelle design (`sharepoint_folder_search` con `design`, poi con `kit`) e raggruppa i risultati per sito (`/sites/<nome>/` in `webUrl`). Quei siti sono i progetti già pronti: proponili con i pulsanti (al massimo 4, più "Altro").
- La ricerca per nome restituisce anche molte sottocartelle (es. 300 risultati per "Turno"): non scorrerle tutte, ti basta il primo risultato con il sito giusto per avere il `driveId`.

### 2. Trovare la cartella design

Leggi la radice della raccolta: `read_resource` con `file:///{driveId}/` (la barra finale elenca la radice). Cerca, in quest'ordine, una cartella il cui nome:

1. è `design-system`, `Design-system`, `Design system` o `DesignSystem`;
2. è `<Progetto>-Design` o `<Progetto>-design-kit`;
3. contiene `design` (senza distinguere maiuscole e minuscole).

Se la cartella trovata non contiene direttamente `README.md` o `styleguide.html` ma ha una sottocartella `design-system`, entra lì (es. `Turno-design-kit/design-system`). Una cartella `marketing` allo stesso livello contiene i testi approvati.

Puoi indirizzare i file anche per percorso, spazi compresi, senza conoscere l'id: `file:///{driveId}/Turno-design-kit/design-system/styleguide.html`. Il percorso parte dalla radice della raccolta: non mettere "Documenti condivisi" o "Shared Documents".

### 3. Confermare

La cartella giusta contiene almeno uno tra `README.md`, `styleguide.html` e `tokens.json`. Se ce ne sono più di una, usa quella modificata più di recente e cita le altre nella proposta. Se non ne trovi nessuna, cerca cartelle come `Marketing`, `Screenshot`, `assets`, `Loghi` (vecchia organizzazione, solo materiale di riferimento) e di' all'utente che il progetto non ha ancora una cartella design secondo le linee guida. Se non basta, mostra le cartelle della radice e chiedi quale contiene le grafiche.

Tutto quello che serve sul progetto (prodotto, pubblici, tono, regole del logo, cose da evitare) è nel `README.md` della cartella design, non in questo file.

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

Le linee guida complete per chi prepara la cartella sono in `linee-guida-cartella-design.md`, e il comando `/crea-cartella-design` la genera dalle repo frontend in locale. La struttura attesa è:

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
