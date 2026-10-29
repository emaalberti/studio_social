# Marketing Assistant — plugin Claude

Genera contenuti social (post, caroselli, storie, reel animati) partendo dalle grafiche ufficiali di ogni progetto, salvate su SharePoint/OneDrive.

Il plugin ha due comandi:

| Comando | A cosa serve | Quando |
|---|---|---|
| `/marketing-assistant:crea-cartella-design` | crea la cartella design di un progetto leggendo le sue repo frontend in locale | **una volta per progetto**, prima di fare post |
| `/marketing-assistant:genera-contenuto` | crea il post usando quella cartella | ogni volta che serve un contenuto |

Il plugin lavora in **sola lettura** sulle fonti aziendali: non crea né modifica nulla su SharePoint, OneDrive o Confluence.

## Installazione

In un terminale:

```bash
claude plugin marketplace add emaalberti/studio_social
claude plugin install marketing-assistant@devnut-studio
```

Il plugin vale sia per Claude Code da terminale sia per la scheda Code dell'app desktop.

### Primo utilizzo (ogni collega)
1. Su claude.ai → Settings → Connectors → **Microsoft 365** → Connect, con l'account aziendale.
2. Riavvia la sessione di Claude: gli strumenti del connettore compaiono da soli.
3. Per Confluence: in una sessione `claude` da terminale lancia `/mcp` e autorizza `marketing-assistant:atlassian`.

Il plugin vede solo i file a cui quella persona ha già accesso.

### Per l'amministratore
- **claude.ai**: Admin settings → Connectors → abilita **Microsoft 365** per l'organizzazione.
- **Microsoft 365 / Entra ID**: concedi il consenso amministrativo all'app Claude nel tenant. È il passaggio che più spesso blocca i colleghi al primo collegamento ("richiede l'approvazione dell'amministratore").

## 1. Preparare la cartella design di un progetto

`/genera-contenuto` funziona solo se il progetto ha una cartella design fatta secondo le linee guida. Il motivo: il connettore Microsoft 365 non scarica file, quindi nel post finisce solo ciò che Claude legge come testo. Serve soprattutto un `styleguide.html` con logo, illustrazioni e icone scritti dentro come SVG. PNG e `.svg` separati non bastano.

Se il progetto ha un frontend:

1. Assicurati di avere sul computer le repo frontend del progetto, aggiornate al branch giusto (es. `git pull` su `main`). Claude non le scarica da GitHub.
2. Apri Claude Code e lancia `/marketing-assistant:crea-cartella-design <Progetto> <percorso della repo>`, per esempio `/marketing-assistant:crea-cartella-design MyTutor ~/code/mytutor-web`. Se le repo sono più di una (es. web e app), indica tutti i percorsi. Se Claude Code è già aperto dentro la repo, basta il nome del progetto.
3. Claude legge il codice (token, config Tailwind, CSS, logo, icone, illustrazioni, componenti, testi), ti mostra cosa ha trovato e cosa manca, poi crea la cartella `~/Desktop/<Progetto>-design/` con dentro `design-system/` e `marketing/`, e la verifica. Le repo restano come sono: Claude le legge e basta.
4. Completa a mano quello che Claude segna come "Da completare", soprattutto i testi approvati.
5. Copia e incolla `design-system/` e `marketing/`, **come cartelle e non zippate**, nella cartella del progetto su OneDrive: il sito SharePoint del progetto (es. `devnut.sharepoint.com/sites/turno`) o la cartella condivisa con il suo nome (es. `Prodotti/MyTutor`). Va bene dal browser o dalla cartella OneDrive sincronizzata nel Finder.
6. Dopo qualche minuto prova con `/marketing-assistant:genera-contenuto post per <Progetto>`.

Le regole complete, con struttura, formati e checklist, sono in [`linee-guida-cartella-design.md`](studio-social/skills/contenuti-social/references/linee-guida-cartella-design.md). Servono anche a chi prepara la cartella a mano.

## 2. Creare un contenuto

Digita `/marketing-assistant:genera-contenuto`, facoltativamente seguito da un'idea (es. `/marketing-assistant:genera-contenuto storia per MyTutor 2.0`).
Claude trova la cartella design del progetto, fa qualche domanda a scelta rapida, propone il concept e produce il visual (Design) o l'animazione, con caption e hashtag.

### Se Claude non trova la cartella
- **Caricata da poco:** la ricerca di SharePoint può impiegare ore a indicizzare file nuovi. Se nel frattempo Claude non la trova, dagli il link della cartella.
- **Esclusa dalla ricerca:** chi amministra il sito deve controllare Impostazioni sito → Ricerca e offline (visibilità nella ricerca attiva) e, nelle impostazioni avanzate della raccolta, usare **Reindicizza raccolta documenti**.
- **Permessi:** chi non ha accesso al sito del progetto non può usarne le grafiche.

## Sviluppo del plugin

### Struttura del repo

```
.claude-plugin/marketplace.json        nome del marketplace (devnut-studio) e cartella del plugin
studio-social/
  .claude-plugin/plugin.json           nome del plugin (marketing-assistant) e versione
  .mcp.json                            server Atlassian per Confluence
  commands/
    genera-contenuto.md                /genera-contenuto: flusso guidato per creare un post
    crea-cartella-design.md            /crea-cartella-design: crea la cartella design dalle repo frontend in locale
  skills/contenuti-social/
    SKILL.md                           regole generali (nascosta dal menu: user-invocable: false)
    references/configurazione.md       come trovare la cartella design su SharePoint
    references/brand.md                regole di brand comuni a tutti i progetti
    references/formati-piattaforme.md  dimensioni e regole del copy per piattaforma
    references/linee-guida-cartella-design.md  struttura, formati e checklist della cartella design
```

### Pubblicare una modifica
1. Modifica i file in `studio-social/`.
2. Aumenta la versione **nello stesso commit**, in due punti: `version` in `studio-social/.claude-plugin/plugin.json` e la riga "marketing-assistant vX.Y.Z" in tutti e due i comandi. Senza una nuova versione Claude non vede le modifiche.
3. Push.
4. Su ogni computer:
   ```bash
   claude plugin marketplace update devnut-studio
   claude plugin update marketing-assistant@devnut-studio
   ```
   poi apri una nuova sessione. In alternativa attiva l'aggiornamento automatico: `/plugin` → Marketplaces → `devnut-studio` → Enable auto-update.
