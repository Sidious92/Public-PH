# Standalone remote gate — PLUS HUB 2.0

**Status:** proposed workflow in a review PR. **Not active until merged into Public-PH main by an authorized maintainer.**

## Why

Remote development from a managed/company PC cannot rely on installing local Node, PostgreSQL or Edge testing utilities. This proposal contains two **manual** workflows: the baseline synthetic Node test suite and a separate PostgreSQL 16 disposable integration gate in a GitHub-hosted runner, directly from **one private source repository `Sidious92/PLUS-HUB`**, without any Shift Planner / Shift Report legacy checkout.

The workflow does not certify Home Lab UI/Edge, real PostgreSQL concurrency, authentication, PDF or company deployment.

## One-time activation (subject to repository review)

1. Review the workflow `.github/workflows/plus-hub-standalone-remote-gate.yml` and the security assumptions below.
2. Merge the **Public-PH** workflow PR to `main` only after explicit maintainer approval. This workflow does **not** run automatically.
3. Ensure existing secret `PUBLIC_PH_READ_TOKEN` grants **read-only Contents** to the private `Sidious92/PLUS-HUB` repository. Never paste or log the secret.

## Per-PR verification from the corporate PC

1. Go to `Sidious92/Public-PH` → **Actions** → **PLUS HUB - Standalone remote regression gate**.
2. **Run workflow** on `main`; paste the **reviewed exact 40-character head commit SHA** from the PLUS-HUB PR into `source_sha`.
3. Check run result and record it beside that SHA in the source PR and issue #61.
4. If the PR receives another commit, **rerun on the new SHA**. Tests from an earlier SHA do not validate a later head.
5. A PASS is only **REMOTE synthetic Node**, not enterprise or Windows acceptance.

Useful candidate PRs to verify separately: PLUS-HUB #64 (Planner UI), #65 (Planner authorized read), #66 (Report foundation), #67 (Report Handover domain) and #68 (Report Handover BFF). They are stacked on different bases: do not treat all heads as one release until their bases have passed their own gates.

## Security constraints

- **Manual dispatch only on Public-PH main.** No external PR trigger, automatic branch push, cron, deployment or workflow chaining.
- Only a 40-character SHA is accepted, and the checked-out SHA is verified before executing tests. No arbitrary shell string is passed as a command.
- Only `Sidious92/PLUS-HUB` is checked out with `persist-credentials:false`, `submodules:false`, `lfs:false`, sparse source roots and no data folders.
- Only Node 22 and the existing `npm test` script are executed; no package install or download of external legacy apps. No artifacts, code bundles or raw test logs are uploaded to Public-PH.
- **Important residual risk:** code executed in CI still runs on a networked worker. A malicious commit could read/exfiltrate the private source in that worker. Only submit *reviewed, trusted commits*; do not allow untrusted contributors to supply SHAs. `PUBLIC_PH_READ_TOKEN` must remain least-privileged and protected under the repo's maintainer/security controls.
- Synthetic unit tests alone are not proof that an authorization scheme or database implementation is production safe. Production controls require independent IT/Privacy approval.

The workflow remains **inert until explicitly merged and manually executed**.

## Report R3 — Disposable PostgreSQL 16 (separate manual workflow)

A second candidate workflow, `.github/workflows/plus-hub-report-pg-ephemeral.yml`, checks out a **reviewed exact PLUS-HUB SHA** into an ephemeral GitHub runner, starts a new PostgreSQL 16 service database named `plus_hub_report_test` with **synthetic fixed CI-only credentials**, installs pinned workspace dependencies without lifecycle scripts and runs `tests/report-postgres/handover-driver-integration.mjs`.

The integration gate deliberately refuses any host other than loopback/`postgres`, requires explicit `PLUS_HUB_EPHEMERAL_REPORT_PG=yes`, and rejects database names outside the `plus_hub_report_test` namespace or a previously populated database. It exercises real atomic event/audit/receipt writes, rollback, scoped permissions and two separate Node workers racing on one aggregate.

As with the synthetic gate, this workflow **is not available to run until the PR has been reviewed, approved, and merged to Public-PH main**. Even then, it runs only when a maintainer manually enters a reviewed exact SHA. No automatic runs, public artifacts or private raw logs. The disposable database is a GitHub-hosted service, never an existing/company PostgreSQL instance.

**Additional residual risk:** installation of npm dependencies on an online runner may access the npm registry; versions are pinned at workspace level but the dependency graph is not yet locked with a checked-in lockfile. Before promoting this workflow as a routine security gate, a reproducible dependency lock and supply-chain review should be ratified.
