# Tasks: Platform Documentation Scaffold

## Review Workload Forecast

| Field | Value |
|-------|-------|
| Estimated changed lines | ~950–1,150 (28 new files: config/workflow ~150, README/index/nav pages ~150, migrated architecture ~350, diagrams ~180, 9 ADRs ~350, open-questions ~40) |
| 400-line budget risk | High |
| Chained PRs recommended | Yes |
| Suggested split | PR 1 → PR 2 → PR 3 → PR 4 → PR 5 → PR 6 (see Work Units) |
| Delivery strategy | auto-chain |
| Chain strategy | pending — needs your decision (see below) |

Decision needed before apply: No
Chained PRs recommended: Yes
Chain strategy: pending
400-line budget risk: High

### Chain strategy decision needed

`auto-chain` splits automatically once the 400-line budget is at risk (it is: ~950–1,150 lines across 28 files). `chain_strategy` is still unset. Pick one before `sdd-apply`:

- **stacked-to-main** — each PR merges to `main` in order; fastest iteration.
- **feature-branch-chain** — a tracker branch accumulates the work; only the tracker merges to `main`; best rollback control.
- **size-exception** — one PR, maintainer-approved; simplest but breaks the 400-line budget outright.

### Suggested Work Units

| Unit | Goal | Likely PR | Focused test command | Runtime harness | Rollback boundary |
|------|------|-----------|----------------------|-----------------|-------------------|
| 1 | Config + workflow + manual-step task + Home nav | PR 1 | `mkdocs build --strict` (nav: Home only) | N/A — docs-only; CI run itself is the harness after merge | Revert PR 1 commits; nothing else depends on it yet |
| 2 | README, ADR template/index, diagrams index, nav additions | PR 2 | `mkdocs build --strict` | N/A — docs-only | Revert PR 2; PR 1 config unaffected |
| 3 | Migrated architecture pages (5 files), nav additions | PR 3 | `mkdocs build --strict` | N/A — docs-only | Revert PR 3; independent content, no downstream refs yet |
| 4 | Diagram pages (4 files), nav additions | PR 4 | `mkdocs build --strict`; `mkdocs serve` visual Mermaid check | N/A — docs-only | Revert PR 4; architecture pages keep their prose without diagram links resolving |
| 5 | ADR set (9 files), nav additions | PR 5 | `mkdocs build --strict` | N/A — docs-only | Revert PR 5; independent of PR 3/4 content |
| 6 | Open questions page + final strict build + deploy verification | PR 6 | `mkdocs build --strict` (full nav); post-merge deploy check | Post-merge: confirm `deploy` job green and site reachable | Revert PR 6; requires Phase 1 manual step already done |

Each unit's `mkdocs.yml` nav additions are scoped to the files it introduces, so every PR passes `mkdocs build --strict` independently regardless of chosen chain strategy. Units 3 and 5 are the densest (migrated content, ADR facts) and may auto-split further at apply time if a single diff still exceeds 400 lines.

## Phase 1: Manual Prerequisite (Non-Automatable) — Satisfies: docs-site-publishing/Manual Pages Source Activation

- [x] 1.1 Repo admin sets GitHub → Settings → Pages → Build and deployment → Source → "GitHub Actions" on `https://github.com/sisques-labs/platform`, before the first push to `main` touching `docs/`. Manual; cannot be scripted; the `deploy` job fails otherwise. Confirmed done by the user 2026-09-10.

## Phase 2: Config & Workflow Foundation — Satisfies: docs-site-publishing/MkDocs + Material Site Configuration, Build and Deploy Workflow

- [x] 2.1 Create `mkdocs.yml` per design.md target shape (Material theme, `pymdownx.superfences` Mermaid, search, `validation:` strict block); nav starts with Home only, grows per work unit.
- [x] 2.2 Create `requirements-docs.txt` pinning exact `mkdocs`/`mkdocs-material` versions resolved to latest stable at apply time.
- [x] 2.3 Create `.github/workflows/docs.yml` per design.md: build job (`mkdocs build --strict`, `upload-pages-artifact`), deploy job (`deploy-pages`, `main`-only, least-privilege `permissions`).

## Phase 3: Site Entry & Structure — Satisfies: platform-docs-structure/Repository README Overview and Map, Documentation Directory Structure, Documentation Navigation Contract

- [x] 3.1 Create `README.md`: platform overview + repo map (Gardenia, Nexora, Sisques Account, account-api, account-web, Portero, one line each) + link to the docs site (migration row 15).
- [x] 3.2 Create `docs/index.md`: site home with entry links (migration row 15). Landed in PR1 (minimal, no orphan sections) to satisfy the Home-only nav build gate; entry links populated incrementally as each section lands (PR2 README/ADR/Diagrams index, PR3 Architecture, PR6 Open Questions).
- [x] 3.3 Create `docs/adr/template.md` (MADR-lite: Context/Decision/Alternatives/Consequences) and `docs/adr/index.md` (ADR listing).
- [x] 3.4 Create `docs/diagrams/index.md`: diagram set landing page.

## Phase 4: Migrated Architecture Pages — Satisfies: account-architecture-docs/Migrated Architecture Pages

- [x] 4.1 Create `docs/architecture/index.md`: Naming + Scope and Principles, translated to English (migration rows 1–2).
- [x] 4.2 Create `docs/architecture/sisques-account/index.md`: service architecture, identity provider, stack, repositories (rows 4, 5, 7, 11); link system-context diagram. Diagram link deferred to PR4 (plain-text reference in PR3, converted to a real link once `diagrams/system-context.md` lands).
- [x] 4.3 Create `docs/architecture/sisques-account/tenancy.md`: two-layer tenancy, "tenant, never space" rule, fixed `owner` meaning (row 3); link ADR-0004. ADR link deferred to PR5 (plain-text reference in PR3, converted to a real link once the ADR lands).
- [x] 4.4 Create `docs/architecture/sisques-account/sessions-and-tokens.md`: session/token model, verbatim identifiers (`access_token`, `refresh_token`, `Domain=.sisqueslabs.com`, 10–15 min TTL, `GET /.well-known/jwks.json`, `GET /api/token`, `POST /refresh`) (row 6); link login-token-issuance and refresh-rotation diagrams and ADR-0002/0005/0006/0008. Links deferred to PR4/PR5 as above.
- [x] 4.5 Create `docs/architecture/sisques-account/data-model.md`: schema (`app`, `user`, `tenant`, `tenant_membership`, `tenant_invite`), invitation flow, `platform_admin` bootstrap (rows 8–10); link data-model-er diagram and ADR-0007. Links deferred to PR4/PR5 as above.
- [x] 4.6 Create `docs/architecture/sisques-account/status-and-mvp.md`: current status and MVP scope, verified facts — `account-platform-mvp` archived, account-web allowlist shipped, gardenia-api migration not started (rows 12–13).

## Phase 5: Diagram Pages — Satisfies: platform-docs-structure/Documentation Directory Structure; docs-site-publishing/Mermaid diagrams render

- [x] 5.1 Create `docs/diagrams/system-context.md` (Mermaid `flowchart`, row 4) and `docs/diagrams/data-model-er.md` (Mermaid `erDiagram`, row 8).
- [x] 5.2 Create `docs/diagrams/login-token-issuance.md` and `docs/diagrams/refresh-rotation.md` (Mermaid sequence diagrams, row 6).

## Phase 6: ADR Set — Satisfies: account-architecture-docs/ADR Coverage for Locked Decisions

- [x] 6.1 Author `docs/adr/0001-sisques-account-owns-identity.md` and `docs/adr/0003-idp-behind-swappable-adapter.md` (rows 4–5), MADR-lite template.
- [x] 6.2 Author `docs/adr/0002-account-issues-its-own-jwt.md`, `docs/adr/0005-jwks-endpoint-key-distribution.md`, `docs/adr/0006-refresh-rotation-reuse-detection.md`, `docs/adr/0008-cookies-scoped-to-parent-domain.md` (row 6), MADR-lite template.
- [x] 6.3 Author `docs/adr/0004-two-layer-tenancy-model.md` and `docs/adr/0007-platform-admin-bootstrap-via-env.md` (rows 3, 10), MADR-lite template.
- [x] 6.4 Author `docs/adr/0009-mkdocs-material-docs-site.md` documenting D1 (tooling choice, from design.md rationale), MADR-lite template.

## Phase 7: Open Questions — Satisfies: account-architecture-docs/Open Questions Page Records the Gardenia JWT Conflict

- [x] 7.1 Create `docs/open-questions.md`: gardenia-api JWT-embedding conflict vs. Sisques Account tenancy model, explicitly labeled unresolved, no resolution stated (row 14).

## Phase 8: Structural Verification — Satisfies: docs-site-publishing/Config builds with strict mode, Workflow fails closed on broken links, Single Source of Content; platform-docs-structure/No orphan pages at build time

- [x] 8.1 Run `mkdocs build --strict` on the full tree; expect zero warnings and zero errors. Done — zero warnings/errors on the full 22-page tree (`mkdocs==1.6.1`, `mkdocs-material==9.7.7`, local venv).
- [x] 8.2 Run `mkdocs serve`; visually confirm all 4 diagram pages render as Mermaid diagrams, not raw fenced text. Done via structural equivalent: built HTML for all 4 diagram pages confirmed containing `<pre class="mermaid">` (Material's Mermaid render target), not raw fenced code blocks.
- [x] 8.3 Confirm no orphan pages: every file under `docs/` appears in `mkdocs.yml` `nav`, per `validation.omitted_files`. Done — all 22 files under `docs/` exactly match the 22 nav `.md` targets; `mkdocs build --strict` also enforces this natively (`omitted_files: warn` + `--strict`).
- [x] 8.4 Walk migration table rows 1–15 against `/Users/javi/Documents/projects/sisques-labs/sisques-account-architecture.md` (read-only) for content fidelity — no decided fact lost or altered. Done — all 15 rows migrated; spot-checked verbatim survival of `access_token`, `refresh_token`, `Domain=.sisqueslabs.com`, `GET /.well-known/jwks.json`, `PLATFORM_ADMIN_EMAILS`, schema tables/UNIQUE constraints, repo/stack names.
- [x] 8.5 Search the repo tree for markdown content outside `docs/` and `README.md`; confirm no duplicated architecture/ADR/diagram content. Done — only `README.md` exists as markdown outside `docs/` in the tracked source tree; no duplication found.
- [x] 8.6 After Phase 1 manual step and merge to `main`: confirm the `deploy` job succeeds and `https://sisques-labs.github.io/platform/` is reachable. Confirmed 2026-09-10: all 6 PRs merged to `main`, workflow run 34453773816 (`docs`) completed with `success`, `https://sisques-labs.github.io/platform/` returns HTTP 200.
