# Public PH — CI pubblica per PLUS HUB 2.0

Questo repository è un **runner CI pubblico**. I sorgenti applicativi restano nei repository privati `Sidious92/PLUS-HUB`, `Sidious92/Shift-Report-New` e `Sidious92/Shift-Planner`.

## Principio di sicurezza

- **Non** committare qui codice privato, dati ospiti, dati HR, PDF, log operativi o token.
- La CI pubblica esegue solo test scelti, su un **commit SHA esatto** di PLUS-HUB privato.
- Le esecuzioni e i **log di GitHub Actions sono pubblici**: il workflow iniziale esegue soltanto la suite delle fixture sintetiche, con output minimale e nessun artifact.
- Nessun trigger su PR pubbliche o codice esterno, nessun `pull_request_target`, nessun uso di token in processi di test.
- Non associare mai automaticamente un risultato di test sulle fixture a un PASS di sicurezza, legacy runtime, parità applicativa o produzione.

## Configurazione richiesta (da fare nelle impostazioni GitHub)

1. Crea un **fine-grained personal access token** con accesso esclusivamente a `Sidious92/PLUS-HUB` e permesso **Contents: Read-only**.
2. In questo repository pubblico, vai su **Settings → Secrets and variables → Actions → New repository secret**.
3. Nome esatto del secret: **`PLUS_HUB_READ_TOKEN`**. Incolla il valore solo nel campo GitHub, non in chat o nei file.
4. Apri **Actions → PLUS HUB — Synthetic fixture integrity → Run workflow** sul branch `main`, impostando il commit SHA di PLUS-HUB che vuoi verificare.
5. Controlla il risultato. Un PASS dimostra soltanto **integrità delle fixture** sul commit selezionato.

## Stato iniziale

Suite disponibile nella PR privata [PLUS-HUB #50](https://github.com/Sidious92/PLUS-HUB/pull/50):
`verification/shift-report/fixture-integrity.test.mjs`.

SHA di esempio verificato: `8d9734024585e286646c2389adc04b49262cd6c9` (non garantisce che questo SHA rimanga il più recente).

Il probe sul **codice originale** di Shift-Report-New richiederà un ulteriore accesso read-only al repository legacy e un secondo workflow isolato; non è incluso in questo bootstrap.

## Limitazioni

- Il runner è pubblico e GitHub-hosted: non usare test che stampano segreti, payload privati o stack trace sensibili.
- La connettività e la disponibilità del secret non possono essere inferite dalla presenza del file YAML.
- L'ultimo tentativo sul repository privato [run 44092049244](https://github.com/Sidious92/PLUS-HUB/actions/runs/44092049244) è fallito prima di assegnare il runner, coerentemente con il problema dei minuti Actions privati esauriti. **Questo nuovo workflow è un'alternativa di esecuzione, non una prova già riuscita.**
- In futuro, per eseguire test su commit privati contenenti cambiamenti non revisionati, introdurre una allowlist di commit approvati e verifiche supplementari.
