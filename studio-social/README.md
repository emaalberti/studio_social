# Studio Social — plugin Claude

Genera contenuti social partendo dalle grafiche ufficiali dell'app.

## Uso
Digita `/genera-contenuto` (facoltativamente seguito da un'idea, es. `/genera-contenuto lancio dark mode`).
Claude si collega a OneDrive o Confluence, fa qualche domanda a scelta rapida, propone il concept e produce il visual (Design) o l'animazione, con caption e hashtag.

## Prima di distribuirlo (amministratore)
1. In `skills/contenuti-social/references/configurazione.md` aggiorna la sezione "Progetti noti" con i percorsi reali delle grafiche di ogni progetto (cartella Shared o sito SharePoint).
2. Se serve, adatta tono di voce e regole "Da evitare" in `skills/contenuti-social/references/brand.md` (colori e font si ricavano per progetto).
3. Abilita l'accesso a OneDrive tramite il connettore **Microsoft 365** di claude.ai (il plugin non include un server MCP per OneDrive: quelli Microsoft sono specifici per tenant e richiedono una app Entra registrata):
   - **Amministratore claude.ai**: Admin settings → Connectors → abilita **Microsoft 365** per l'organizzazione.
   - **Amministratore Microsoft 365 / Entra ID**: concedi il consenso amministrativo all'app Claude nel tenant, così i colleghi non si bloccano su "richiede l'approvazione dell'amministratore".
   - Per Confluence il server Atlassian è già incluso in `.mcp.json`.
4. Carica il plugin nel catalogo dell'organizzazione.

## Primo utilizzo (ogni collega)
1. Su claude.ai → Settings → Connectors → **Microsoft 365** → Connect, e accedi con l'account aziendale.
2. Usa Claude Code con il login claude.ai: gli strumenti del connettore compaiono automaticamente (riavvia la sessione dopo il collegamento).
3. Per Confluence, al primo uso autentica il server Atlassian con `/mcp`.

Il plugin vede solo i file a cui quella persona ha già accesso su OneDrive/Confluence.

Quando chiedi all'amministratore di abilitare il connettore, ricordagli anche il consenso amministrativo in Entra ID. È il passaggio che più spesso blocca i colleghi al primo collegamento.