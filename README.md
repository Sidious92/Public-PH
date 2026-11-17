# Public-PH — Runner CI pubblico di PLUS HUB 2.0

Questo repository contiene **solo workflow e istruzioni CI**, non codice proprietario, file aziendali, database, PDF, dati degli ospiti o informazioni HR.

Gli applicativi e i test restano nei repository privati. I job pubblici fanno checkout su commit SHA esatti, con token di sola lettura, disabilitano la persistenza delle credenziali Git e non caricano artifact o log sorgente.

## Workflow disponibili

| Workflow | Controllo | Stato effettivamente verificato |
|---|---|---|
| [Fixture sintetiche](.github/workflows/plus-hub-fixtures.yml) | `PLUS-HUB` PR #50, schema delle fixture | **PASS**: [run 44177186028](https://github.com/Sidious92/Public-PH/actions/runs/44177186028) |
| [Scaffold e import boundaries](.github/workflows/plus-hub-architecture.yml) | `PLUS-HUB` PR #47, test architetturali | **PASS**: [run 44177393214](https://github.com/Sidious92/Public-PH/actions/runs/44177393214) |
| [Motori legacy](.github/workflows/plus-hub-legacy-engines.yml) | Esecuzione effettiva del source legacy di Report e Planner | **BLOCCATO dai permessi**: [run 44178323845](https://github.com/Sidious92/Public-PH/actions/runs/44178323845) |

Il PASS su fixture o scaffold **non** significa parità funzionale, sicurezza in produzione o GO alla migrazione.

## Secret e permessi

Il repository ha già il secret GitHub Actions **`PUBLIC_PH_READ_TOKEN`**, che ha consentito di leggere `Sidious92/PLUS-HUB`. Il suo valore non deve essere copiato nei file, nella chat, nelle issue o nei log pubblici.

Il nuovo workflow di caratterizzazione dei motori ha provato ad accedere a due ulteriori repository e ha ottenuto **HTTP 403** su entrambi. Pertanto, per usare la CI centralizzata, il fine-grained PAT referenziato dal secret deve avere:

- **Repository access:** esattamente `Sidious92/PLUS-HUB`, `Sidious92/Shift-Report-New`, `Sidious92/Shift-Planner`.
- **Repository permissions:** `Contents: Read-only` (e permessi Metadata predefiniti).
- Nessun permesso Actions Write, Administration, Secrets, Pull Requests Write o accesso agli altri repository.

Se il PAT è modificabile, aggiorna l'elenco dei repository selezionati nel pannello GitHub del token. Se viene rigenerato con un valore differente, **aggiorna il valore** del secret `PUBLIC_PH_READ_TOKEN` in [Public-PH Actions Secrets](https://github.com/Sidious92/Public-PH/settings/secrets/actions). Non creare un token classic con accesso globale solo per questa CI.

Questa verifica riguarda **i permessi di checkout**, non è un errore del codice del dominio.

## Avvio manuale dopo la correzione

1. Apri [Actions di Public-PH](https://github.com/Sidious92/Public-PH/actions).
2. Scegli **PLUS HUB - Legacy domain engine characterization**.
3. Premi **Run workflow** dal branch `main`.
4. Controlla separatamente i due job:
   - **Shift Report:** checkout dello SHA `777bb52eecea605ef8dcbba491e2f20d85898663`, esecuzione del `probe-legacy.mjs` presente in `PLUS-HUB` allo SHA `8d9734024585e286646c2389adc04b49262cd6c9` su moduli originali di data operativa, checklist, codec Notte e pacchi.
   - **Shift Planner:** checkout dello SHA `ccf723a6bbc8421c8bf6eb77466313b48c52539a`, esecuzione di tre suite Node originali su modello/validatori, richieste settimanali e annullamento amministrativo delle assenze.
5. L'esito viene riportato con un messaggio generico. Nessun file, stack trace o output privato viene caricato come artifact.

**Stato:** i due job hanno potuto inizializzarsi sul runner pubblico, ma il primo test di checkout legacy è stato negato con `403`. **Nessun test dei motori originali è stato eseguito tramite questo workflow.**

## Limiti e sicurezza

- Solo avvio manuale: niente trigger da PR pubbliche e niente `pull_request_target`.
- Il workflow usa SHA di sorgente **fissati nel file YAML**, non branch privati o ref arbitrari forniti dagli utenti. Per aggiornare le baseline serve una modifica revisionata del workflow.
- I file di lavoro rimangono sul runner temporaneo e non vengono pubblicati; ogni step di esecuzione dei test ha un output generico.
- Un repository pubblico che può leggere codice privato richiede amministrazione prudente: solo persone fidate devono poter modificare workflow e lanciare test, senza consentire PR non attendibili a job con secret.
- CI green su moduli legacy ≠ equivalenza di PLUS HUB 2.0. Servono ancora confronto con versione installata, test BFF, autorizzazione, concorrenza, PDF e backup/restore.

**Decisione architetturale:** mantenere motori sorgente privati e riusarli solo dopo caratterizzazione; niente copia indiscriminata di UI, database OneDrive, identità locale o parti di applicazione dipendenti dal DOM.
