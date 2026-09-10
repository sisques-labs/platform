# Verify Report: platform-docs-scaffold

**Date**: 2026-09-10
**Verdict**: PASS WITH WARNINGS
**Mode**: Full artifacts (proposal, specs, design, tasks) — documentation-only change, structural verification per Verification Note

## Verification Method

This is a documentation-only change spread across 6 stacked, unmerged PRs (PR1→main, PR2→PR1, PR3→PR2, PR4→PR3, PR5→PR4, PR6→PR5). Verification was performed against the actual GitHub state, not solely the local working tree:

- Confirmed PR base chain via `gh pr list --json baseRefName,headRefName` (all correct, see Structural Checks).
- Confirmed all 6 PRs are `MERGEABLE`/`CLEAN` with no conflicts via `gh pr list --json mergeable,mergeStateStatus`.
- Confirmed each PR's own `build` CI check passes independently via `gh pr checks {1..6}` (all `pass`; `deploy` correctly `skipping` since none are on `main` yet).
- Checked out `pr6/open-questions-and-verification` locally (contains the cumulative tree — all 23 files under `docs/`, `README.md`, `mkdocs.yml`, `requirements-docs.txt`, `.github/workflows/docs.yml`, `.gitignore`).
- Re-installed `mkdocs==1.6.1` and `mkdocs-material==9.7.7` from `requirements-docs.txt` into a fresh venv (versions match exactly) and re-ran `mkdocs build --strict` myself — did not trust the apply report's claim.
- Diffed migrated content against the source `sisques-account-architecture.md` for verbatim fact preservation.
- Diffed per-PR file scopes (`gh pr diff --name-only`) to check for scope drift/duplication.

## Completeness (Tasks)

25/27 tasks checked `[x]`. 2 remain unchecked and are **expected, disclosed non-blockers**, not gaps:

| Task | Status | Why it cannot complete now |
|---|---|---|
| 1.1 — Set GitHub Pages Source to "GitHub Actions" | `[ ]` | Requires a human with repo admin access via the GitHub UI; no agent can perform this. Correctly modeled in `tasks.md` as Phase 1 "Manual Prerequisite (Non-Automatable)" and satisfies the spec's own "Manual Pages Source Activation" requirement, which is satisfied by *documenting* the manual step, not by any file. |
| 8.6 — Confirm `deploy` job green + site reachable | `[ ]` | Blocked transitively on 1.1 and on all 6 PRs merging to `main`, neither of which has happened — work is currently in 6 open, unmerged PRs. |

All 25 other tasks were independently verified against real file content and/or GitHub state below, not just trusted from the checkbox.

## Structural Checks (GitHub state)

| Check | Result |
|---|---|
| PR base chain (PR1→main, PR2→PR1, PR3→PR2, PR4→PR3, PR5→PR4, PR6→PR5) | **Correct** — verified via `gh pr list --json baseRefName,headRefName` |
| PR mergeability | All 6 `MERGEABLE` / `CLEAN`, no conflicts |
| Per-PR CI `build` job | All 6 `pass`; `deploy` correctly `skipping` (gated to `main` only, per design D5/`docs.yml`) |
| File scope overlap across PRs | `mkdocs.yml` and `docs/index.md` appear in nearly every PR's diff, and PR4/PR5 touch some architecture pages PR3 created. This is **intentional, documented incremental linking** (tasks 4.2–4.5 explicitly note "link deferred to PR4/PR5, converted from plain-text reference once the target lands"), not a genuine conflict — confirmed no two PRs create the same file, and all 6 PRs are cleanly mergeable in sequence. |
| Build artifacts (`site/`, `.venv-docs/`) tracked in git? | No — both gitignored and untracked (`git ls-files` confirms) |

## Build Evidence (re-run, not trusted from apply report)

```
$ pip install -r requirements-docs.txt   # fresh venv
mkdocs        1.6.1      (matches requirements-docs.txt exactly)
mkdocs-material 9.7.7    (matches requirements-docs.txt exactly)

$ mkdocs build --strict -d <scratch-dir>
INFO    -  Cleaning site directory
INFO    -  Documentation built in 0.47 seconds
EXIT: 0
```
Zero warnings, zero errors. (The Material-for-MkDocs vendor advisory banner about MkDocs 2.0 is a startup notice, not a build warning/error, and does not affect the exit code.)

- All 4 Mermaid diagram pages (`system-context`, `login-token-issuance`, `refresh-rotation`, `data-model-er`) confirmed rendering as `<pre class="mermaid">` in built HTML — not raw fenced text.
- Built site contains 25 HTML pages; 22 markdown source files under `docs/` each appear as an exact `nav` target in `mkdocs.yml`; `--strict` + `validation.omitted_files: warn` enforces no orphans natively (this passed).
- No markdown outside `docs/` and `README.md` in the tracked tree except `openspec/` SDD process artifacts (not site content, not duplicated architecture/ADR/diagram content).

## Spec Compliance Matrix

### platform-docs-structure (3 requirements, 5 scenarios)

| Requirement / Scenario | Status | Evidence |
|---|---|---|
| Repository README Overview and Map / README enumerates all known repos | PASS | `README.md` repo map table lists Gardenia, Nexora, Sisques Account, account-api, account-web, Portero, each with a description |
| ... / README links into the docs site | PASS | `README.md` links to `https://sisques-labs.github.io/platform/` and `docs/` |
| Documentation Directory Structure / Required subdirectories exist | PASS | `docs/architecture/`, `docs/adr/`, `docs/diagrams/` each exist with multiple files |
| Documentation Navigation Contract / No orphan pages at build time | PASS | `mkdocs build --strict` re-run clean; 22/22 files match nav targets |
| ... / Directory-to-nav-section mapping holds | PASS | `mkdocs.yml` nav structurally maps `architecture/` → Architecture, `adr/` → Decisions (ADR), `diagrams/` → Diagrams |

### docs-site-publishing (4 requirements, 6 scenarios)

| Requirement / Scenario | Status | Evidence |
|---|---|---|
| MkDocs + Material Site Config / Config builds with strict mode | PASS | Re-run by verifier, exit 0, zero warnings |
| ... / Mermaid diagrams render | PASS | `<pre class="mermaid">` confirmed in built HTML for all 4 diagram pages |
| Single Source of Content / No duplicated content directories | PASS | Only `README.md` + `openspec/` (process, not site content) exist outside `docs/` |
| Build and Deploy Workflow / Workflow runs on push to default branch | PASS (design-level; not yet observed on `main`) | `.github/workflows/docs.yml` matches design.md exactly: pinned install, `mkdocs build --strict`, `deploy` gated `github.ref == 'refs/heads/main'`. Per-PR `build` job passing on GitHub Actions confirms the workflow executes correctly; the `main`-triggered run itself has not happened yet (no PR merged) |
| ... / Workflow fails closed on broken links | PASS (by construction) | `validation:` block + `--strict` fails the build step before `upload-pages-artifact`/`deploy` can run; no broken-link fixture was needed since MkDocs' own strict validation is the enforcement mechanism per design D8 |
| Manual Pages Source Activation / Manual step is explicitly recorded | PASS | Task 1.1 in `tasks.md`, explicitly marked non-automatable, unchecked as expected |

### account-architecture-docs (3 requirements, 5 scenarios)

| Requirement / Scenario | Status | Evidence |
|---|---|---|
| Migrated Architecture Pages / Architecture pages present in English | PASS | All pages under `docs/architecture/` read in English; not raw copies |
| ... / Implementation status recorded accurately | PASS | `status-and-mvp.md`: "`account-platform-mvp` is implemented, verified, and archived"; "`account-web` shipped the cross-domain SSO redirect allowlist"; "Migration of `gardenia-api`... has not started" — matches spec wording exactly |
| ADR Coverage for Locked Decisions / Every locked decision has an ADR | PASS | ADR-0001 (shared identity), ADR-0002+0005 (JWT+JWKS), ADR-0004 (two-layer tenancy), ADR-0003 (Keycloak adapter), ADR-0006 (refresh rotation+reuse), ADR-0007 (`platform_admin` bootstrap), ADR-0008 (cookie scoping) — all 7 locked decisions covered, plus ADR-0009 (tooling, extra) |
| ... / ADRs follow a consistent record format | PASS | All 9 ADRs use identical `Context`/`Decision`/`Consequences` headings (MADR-lite template), `Status: Accepted` |
| Open Questions Page / Gardenia-api JWT conflict present and unresolved | PASS | `docs/open-questions.md`: "**Status: Unresolved.**"..."this page does not resolve the conflict"..."intentionally does **not** propose or imply a resolution" — no resolution stated anywhere |

**Totals**: 10 requirements, 16 scenarios — all 16 PASS.

## Content Fidelity Spot-Check (design.md migration table, 8 of 15 rows checked)

| Row | Fact checked | Source (Spanish) | Target (English) | Result |
|---|---|---|---|---|
| 3 | Two-layer tenancy, "tenant never space" | `Modelo de tenancy` | `tenancy.md` | Verbatim-preserved in meaning, terminology rule intact |
| 6 | `access_token`/`refresh_token`, `Domain=.sisqueslabs.com`, `GET /.well-known/jwks.json`, `GET /api/token`, `POST /refresh`, 10-15min TTL | `Modelo de sesión y tokens` | `sessions-and-tokens.md` | All identifiers verbatim |
| 8 | Schema tables + `UNIQUE(app_id, slug)`, `UNIQUE(tenant_id, user_id)` | `Modelo de datos` | `data-model.md` | Verbatim, all columns/constraints preserved |
| 10 | `PLATFORM_ADMIN_EMAILS` bootstrap | Bootstrap paragraph | `data-model.md` § bootstrap | Verbatim |
| 12/13 | Status facts (`account-platform-mvp` archived, allowlist shipped, gardenia not started) | `Estado`/`MVP` | `status-and-mvp.md` | Matches spec's exact required wording |
| 14 | gardenia-api JWT conflict, unresolved | (not in source, from exploration) | `open-questions.md` | Present, explicitly unresolved, no resolution stated |
| 15 | Repo map + platform overview | (new) | `README.md`, `docs/index.md` | README owns canonical map (D7), all 6 repos present |

No decided fact found lost or altered.

## Scope Drift Check

Proposal's "Out of Scope" list cross-checked against delivered content:

- Gardenia-api vs. Sisques Account JWT/tenancy conflict: **present, explicitly unresolved** — not silently resolved. Confirmed.
- No application code or runtime change in any repo: confirmed — only `docs/`, `README.md`, `mkdocs.yml`, `requirements-docs.txt`, `.github/workflows/docs.yml`, `.gitignore` changed across all 6 PRs.
- Gardenia/Nexora/Portero architecture beyond the README map: not present — those repos only appear in the `README.md` map table with one-line descriptions, no dedicated architecture pages.
- Versioned docs, i18n, custom domain, second IdP adapter: none of these appear anywhere in the delivered tree.

No scope drift found.

## Design Coherence

- `mkdocs.yml` matches design.md's target shape byte-for-byte (theme, plugins, markdown_extensions, validation block, nav tree).
- `.github/workflows/docs.yml` matches design.md's workflow exactly (build/deploy jobs, permissions, concurrency group, path filters).
- ADR template usage matches design.md's template exactly across all 9 ADRs.
- D5 (deploy via `upload-pages-artifact`/`deploy-pages`, no `gh-pages` branch push) confirmed — no branch push logic anywhere in the workflow.
- D7 (README owns canonical repo map, `docs/index.md` links out to it) confirmed in both files.

No design deviations found.

## Issues

### CRITICAL
None.

### WARNING
1. **Requirement "Workflow runs on push to default branch" is verified by construction and per-PR CI, not by an actual `main`-triggered run.** No PR has merged yet, so the `push: branches: [main]` trigger path and the `deploy` job have never executed for real. This is a natural consequence of the work being intentionally left in open PRs for review (task 8.6 already discloses this). Not a defect — flagged so the orchestrator/user tracks it as still-open runtime evidence, closed once PRs merge and task 8.6 completes.
2. **Task 8.4's own claim ("all 15 rows migrated") was independently spot-checked on 8 of 15 rows, not all 15.** All 8 checked rows passed with verbatim fidelity; the remaining 7 rows (1, 2, 4, 5, 7, 9, 11) were not independently re-verified by this pass, though task 8.4 in `tasks.md` reports they were checked at apply time. Recommend a full 15-row pass before archive if maximal certainty is desired, though the sampled rows found zero discrepancies and give no reason to suspect the rest.

### SUGGESTION
1. `.gitkeep` at repo root (visible in the local `ls -la`) appears to be a leftover placeholder from before `openspec/` was populated; it is not referenced by any spec/task and is harmless, but could be removed once the repo has real content (cosmetic only, not a spec violation).

## Known-and-Expected Incomplete Tasks (disclosed non-blockers)

- **Task 1.1** — GitHub Pages Source activation: requires human repo-admin action in the GitHub UI, cannot be scripted by any agent. Explicitly modeled as Phase 1 "Manual Prerequisite (Non-Automatable)" in `tasks.md`, which itself satisfies the spec's "Manual Pages Source Activation" requirement (the requirement is satisfied by recording the task, not by completing it).
- **Task 8.6** — Post-merge deploy-job-green + live-site-reachable check: transitively blocked on task 1.1 and on all 6 PRs merging to `main`. Cannot be completed until human review/merge happens outside this session.

Both are disclosed, structurally correct non-blockers, not verification gaps. They do not affect the PASS WITH WARNINGS verdict above.

## Final Verdict

**PASS WITH WARNINGS**

All 16 spec scenarios across the 3 delta specs are satisfied by the actual cumulative file tree across the 6-PR stack, verified against real GitHub state (PR bases, mergeability, per-PR CI) and a from-scratch `mkdocs build --strict` re-run (not trusted from the apply report). 25/27 tasks complete; the 2 remaining are expected, disclosed, human-gated non-blockers. No scope drift, no design deviations, no CRITICAL issues. The 2 WARNINGs concern runtime evidence that structurally cannot exist yet (no merge to `main` has occurred) and are appropriately deferred to post-merge, matching the change's own Verification Note and Testing Strategy.

## Key Learnings

1. Stacked PR chains require checking out the tip branch to see the cumulative tree for full verification.
2. Re-running the build yourself with pinned dependency versions catches drift that trusting an apply report cannot.
3. MkDocs `validation: omitted_files: warn` combined with `--strict` natively enforces the no-orphan-pages requirement without extra tooling.
4. Manual, non-automatable tasks like GitHub Pages activation should be modeled as their own tasks.md phase so verification can distinguish them from real gaps.
5. Incremental cross-PR file touches (nav, index links) are expected in stacked documentation PRs and are not scope conflicts when each PR remains independently buildable.
