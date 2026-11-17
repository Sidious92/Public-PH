# Public PH — CI pubblica per PLUS HUB 2.0

Questo repository è un **runner CI pubblico**. I sorgenti applicativi restano nel repository privato `Sidious92/PLUS-HUB`; qui sono presenti solo configurazioni di esecuzione e istruzioni non riservate.

## Principio di sicurezza

- **Non** committare qui codice privato, dati ospiti, dati HR, PDF, log operativi o token.
- La CI pubblica esegue solo test scelti, su un **commit SHA esatto** di PLUS-HUB privato.
- Le esecuzioni e i **log di GitHub Actions sono pubblici**: il workflow iniziale esegue soltanto la suite delle fixture sintetiche, con output minimale e nessun artifact.
- Nessun trigger su PR pubbliche o codice esterno, nessun `pull_request_target`, nessun uso di token in processi di test.
- Non associare mai automaticamente un risultato di test sulle fixture a un PASS di sicurezza, legacy runtime, parità applicativa o produzione.

## Configurazione richiesta (da fare nelle impostazioni GitHub)

1. Crea un **fine-grained personal access token** con accesso esclusivamente a `Sidious92/PLUS-HUB` e permesso **Contents: Read-only**.
2. In questo repository pubblico, vai su **Settings → Secrets and variables → Actions → New repository secret**.
3. Nome esatto del secret: **`PUBLIC_PH_READ_TOKEN`**. Incolla il valore solo nel campo GitHub, non in chat o nei file.
4. Apri **Actions** nel repository `Public-PH` e seleziona uno dei due workflow manuali sul branch `main`:
   - **PLUS HUB - Synthetic fixture integrity**: usa lo SHA `8d9734024585e286646c2389adc04b49262cd6c9` (PR #50, salvo avanzamenti).
   - **PLUS HUB - Architecture scaffold checks**: usa lo SHA `4ce5a03e46daa570eb8fd30cc4130db2665c6da4` (PR #47, salvo avanzamenti).
5. Premi **Run workflow** e controlla l'esito. Il primo PASS prova solo l'integrità delle fixture; il secondo PASS prova solo i vincoli statici di scaffold e import.

## Stato iniziale

Suite disponibile nella PR privata [PLUS-HUB #50](https://github.com/Sidious92/PLUS-HUB/pull/50):
`verification/shift-report/fixture-integrity.test.mjs`.

SHA di esempio verificato: `8d9734024585e286646c2389adc04b49262cd6c9` (non garantisce che questo SHA rimanga il più recente).

I test sui **repository legacy** richiederanno credenziali read-only distinte e workflow isolati; non sono inclusi in questo bootstrap.\n\n**Nota sulla riservatezza:** i workflow usano sparse checkout: scaricano solo le sottocartelle strettamente necessarie e non pubblicano artefatti, sorgenti o stack trace. Non passare un commit non revisionato a un job dotato di accesso ai repository privati.

## Limitazioni

- Il runner è pubblico e GitHub-hosted: non usare test che stampano segreti, payload privati o stack trace sensibili.
- La connettività e la disponibilità del secret non possono essere inferite dalla presenza del file YAML.
- L'ultimo tentativo sul repository privato [run 44092049244](https://github.com/Sidious92/PLUS-HUB/actions/runs/44092049244) è fallito prima di assegnare il runner, coerentemente con il problema dei minuti Actions privati esauriti. **Questo nuovo workflow è un'alternativa di esecuzione, non una prova già riuscita.**
- In futuro, per eseguire test su commit privati contenenti cambiamenti non revisionati, introdurre una allowlist di commit approvati e verifiche supplementari.
