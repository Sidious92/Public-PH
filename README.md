# Public-PH — CI pubblica per PLUS HUB 2.0

Repository dedicato ai **runner GitHub Actions pubblici**. I sorgenti dei tre applicativi rimangono privati. Non committare codice proprietario, dati ospiti, documenti HR, PDF, credenziali o output riservati.

## Workflow e risultati verificati — 17–18 novembre 2026

| Workflow | Verifica | Evidenza reale |
|---|---|---|
| [Fixture Shift Report](.github/workflows/plus-hub-fixtures.yml) | struttura delle fixture sintetiche della PR #50 | **PASS** — [run 44177186028](https://github.com/Sidious92/Public-PH/actions/runs/44177186028) |
| [Architettura PLUS HUB](.github/workflows/plus-hub-architecture.yml) | suite dei confini architetturali dello scaffold PR #47 | **PASS** — [run 44177393214](https://github.com/Sidious92/Public-PH/actions/runs/44177393214) |
| [Motori legacy](.github/workflows/plus-hub-legacy-engines.yml) | source effettivo di Shift Report e tre suite Node Shift Planner | **PASS, entrambi i job** — [run 44183858636](https://github.com/Sidious92/Public-PH/actions/runs/44183858636) |
| [Planner fase 1](.github/workflows/plus-hub-planner-extraction.yml) | sei moduli originali, source SHA e confronto differenziale | **PASS** — [run 44222037745](https://github.com/Sidious92/Public-PH/actions/runs/44222037745) |
| [Planner fase 2](.github/workflows/plus-hub-planner-phase2.yml) | 14 moduli, NIGHT cross-week, FERIE/ROL, MALATTIA, editing/finalizzazione e confronto sorgente | **PASS** — [run 44226060383](https://github.com/Sidious92/Public-PH/actions/runs/44226060383) |
| [Planner BFF comandi](.github/workflows/plus-hub-planner-bff.yml) | permessi synthetic, CAS, replay e rollback simulato multiweek; **nessun PostgreSQL connesso** | **PASS** — [run 44291131659](https://github.com/Sidious92/Public-PH/actions/runs/44291131659) |
| [Planner schema PostgreSQL (review)](.github/workflows/plus-hub-planner-schema-review.yml) | schema candidato NON ESEGUITO, static lint + regressioni sintetiche; **nessun DB** | **PASS** — [run 44293448601](https://github.com/Sidious92/Public-PH/actions/runs/44293448601) |

Anche l'ultimo workflow Planner è ripristinato alla modalità manuale; i commit sorgente sono pin.

Il PASS dei motori legacy dimostra **solo i test selezionati sui commit specifici**. Non dimostra equivalenza fra sistemi, sicurezza in produzione, versioni effettivamente distribuite sui PC, PDF visuali o conformità di tutti i gate G0–G8.

## Mappa dei secret — permessi minimi

Tutti sono **Repository Secrets** di `Sidious92/Public-PH`, usati dai job su GitHub-hosted public runner. I valori non sono leggibili o pubblicati.

| Repository sorgente privato | Nome esatto del secret | Permesso |
|---|---|---|
| `Sidious92/PLUS-HUB` | `PUBLIC_PH_READ_TOKEN` | Contents: Read-only |
| `Sidious92/Shift-Report-New` | `PUBLIC_PH_X_SHIFT_REPORT_TOKEN` | Contents: Read-only |
| `Sidious92/Shift-Planner` | `PUBLIC_PH_X_SHIFT_PLANNER_TOKEN` | Contents: Read-only |

**Non sostituire** i tre riferimenti con un token di accesso globale. Non copiare i valori dei secret in chat, Git, job log, issue, artifact o cache.

## Come eseguire il test dei motori

1. Apri [GitHub Actions di Public-PH](https://github.com/Sidious92/Public-PH/actions).
2. Seleziona **PLUS HUB - Legacy domain engine characterization**.
3. Premi **Run workflow** da `main`.
4. Verifica separatamente i due job:
   - **Shift Report:** `Shift-Report-New@777bb52eecea605ef8dcbba491e2f20d85898663` con `PLUS-HUB@8d9734024585e286646c2389adc04b49262cd6c9` per il probe sintetico: date operative, checklist, eventi Notte, pacchi.
   - **Shift Planner:** `Shift-Planner@ccf723a6bbc8421c8bf6eb77466313b48c52539a`; tre suite Node selezionate (suite canonica, request-week reconciler, administrative illness reversal).
5. Il workflow registra PASS/FAIL generici per evitare la diffusione di output privati nel repository pubblico.

L'esecuzione del **17/11/2026** ha superato verifica dei tre token, checkout dei commit e i test selezionati. I sorgenti originali non vengono copiati nel repository pubblico e non sono caricati artifact.

## Misure di sicurezza e limitazioni

- **Trigger manuale soltanto**: niente workflow avviati da PR esterne, `pull_request_target` o input di codice arbitrario.
- Checkout su commit SHA fissati e `persist-credentials: false`; nessun upload artifact/log di test.
- Ai job con token privati possono contribuire soltanto manutentori fidati: la sicurezza dei token dipende anche dalla protezione del branch `main` e dalla revisione dei workflow.
- Questi test possono usare codice proveniente dai repository privati: verificare le modifiche ai commit fissati prima di cambiare gli SHA. Nessun test deve contenere dati reali o stampare informazioni sensibili.
- I workflow di Report e Planner sono **test di caratterizzazione e regressione del legacy**, non una migrazione della logica nel backend di PLUS HUB.
- Restano aperti: mappatura della release usata in struttura, estrazione controllata dei motori, parity test sul target, autorizzazioni BFF e per-record, concorrenza e restore.

**Direzione approvata:** motori legacy riutilizzabili, mantenendo separati Core, BFF, DB e criteri di autorizzazione della piattaforma nuova.
