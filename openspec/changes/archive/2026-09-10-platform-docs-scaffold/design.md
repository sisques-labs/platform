# Design: Platform Documentation Scaffold

## Technical Approach

`docs/` is the single source for both repository browsing and the published site. MkDocs + Material (confirmed by the user over Jekyll/Docusaurus) renders it; a GitHub Actions workflow builds with `mkdocs build --strict` and deploys the artifact through the official Pages Actions pipeline. Content is a translation + restructuring of the Spanish source doc: platform-wide prose into `docs/architecture/`, per-decision prose into numbered MADR-lite ADRs, diagrams into standalone Mermaid pages, and the unresolved gardenia-api conflict into a dedicated Open Questions page.

## Architecture Decisions

| # | Decision | Alternatives rejected | Rationale |
|---|---|---|---|
| D1 | Site generator: MkDocs + Material | Jekyll (no native Mermaid, weak nav), Docusaurus (Node/React/MDX overhead), raw markdown | User-confirmed; plain-markdown `docs/` stays readable on GitHub and in the site |
| D2 | Split Sisques Account into 5 pages under `docs/architecture/sisques-account/` | One monolithic page | Source has 5 distinct concerns; short pages give real nav depth and stable deep-link targets for ADRs |
| D3 | Diagrams as first-class markdown pages in `docs/diagrams/` | Inline Mermaid duplicated per page; standalone `.mmd` files | `.mmd` is not a page — MkDocs will not render it without an include plugin. Markdown pages render natively, are linkable from several pages, and keep each diagram single-sourced |
| D4 | ADRs: MADR-lite, `NNNN-kebab-title.md`, zero-padded from `0001` | Date-prefixed names; ADR text inside architecture pages | Numbering is stable and citable (`ADR-0004`); separation keeps architecture pages narrative and decisions auditable |
| D5 | Deploy via `upload-pages-artifact` + `deploy-pages` | `mkdocs gh-deploy` pushing a `gh-pages` branch | Modern Pages source; no build output committed to git, no bot push permissions, deployment URL surfaced in the run |
| D6 | Pin versions in `requirements-docs.txt` | Unpinned `pip install mkdocs mkdocs-material` in the step | Reproducible builds; a Material major bump cannot silently break `--strict` |
| D7 | Canonical repo map lives in `README.md`; `docs/index.md` links to it | Map in `docs/index.md` with README as pointer; map in both | GitHub landing page must be self-contained, and the map is repository metadata, not architecture. Tradeoff accepted: the map is one click off the site home |
| D8 | Link checking via MkDocs `validation:` + `--strict` | Extra `lychee`/`linkchecker` CI step | Internal links, anchors and orphan pages are all covered natively; no external tool for a repo with no external-link surface yet |

## `mkdocs.yml` (target shape)

```yaml
site_name: Sisques Labs Platform
site_description: Architecture, decisions and diagrams for the Sisques Labs platform
site_url: https://sisques-labs.github.io/platform/
repo_url: https://github.com/sisques-labs/platform
edit_uri: edit/main/docs/

theme:
  name: material
  features: [navigation.sections, navigation.expand, navigation.top, navigation.instant, content.code.copy, search.suggest, search.highlight]
  palette:
    - media: "(prefers-color-scheme: light)"
      scheme: default
      toggle: { icon: material/weather-night, name: Switch to dark mode }
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      toggle: { icon: material/weather-sunny, name: Switch to light mode }

plugins:
  - search

markdown_extensions:
  - admonition
  - attr_list
  - tables
  - toc: { permalink: true }
  - pymdownx.details
  - pymdownx.superfences:
      custom_fences:
        - name: mermaid
          class: mermaid
          format: !!python/name:pymdownx.superfences.fence_code_format

validation:
  omitted_files: warn
  absolute_links: warn
  unrecognized_links: warn
  anchors: warn

nav:
  - Home: index.md
  - Architecture:
      - Platform Overview: architecture/index.md
      - Sisques Account:
          - Overview: architecture/sisques-account/index.md
          - Tenancy Model: architecture/sisques-account/tenancy.md
          - Sessions & Tokens: architecture/sisques-account/sessions-and-tokens.md
          - Data Model: architecture/sisques-account/data-model.md
          - Status & MVP: architecture/sisques-account/status-and-mvp.md
  - Diagrams:
      - Index: diagrams/index.md
      - System Context: diagrams/system-context.md
      - Login & Token Issuance: diagrams/login-token-issuance.md
      - Refresh Rotation: diagrams/refresh-rotation.md
      - Data Model (ER): diagrams/data-model-er.md
  - Decisions (ADR):
      - Index: adr/index.md
      - ADR-0001 Sisques Account owns identity: adr/0001-sisques-account-owns-identity.md
      - ADR-0002 Account issues its own JWT: adr/0002-account-issues-its-own-jwt.md
      - ADR-0003 IdP behind a swappable adapter: adr/0003-idp-behind-swappable-adapter.md
      - ADR-0004 Two-layer tenancy model: adr/0004-two-layer-tenancy-model.md
      - ADR-0005 JWKS key distribution: adr/0005-jwks-endpoint-key-distribution.md
      - ADR-0006 Refresh rotation with reuse detection: adr/0006-refresh-rotation-reuse-detection.md
      - ADR-0007 platform_admin bootstrap via env: adr/0007-platform-admin-bootstrap-via-env.md
      - ADR-0008 Cookies scoped to .sisqueslabs.com: adr/0008-cookies-scoped-to-parent-domain.md
      - ADR-0009 MkDocs + Material for the docs site: adr/0009-mkdocs-material-docs-site.md
      - Template: adr/template.md
  - Open Questions: open-questions.md
```

Every file appears in `nav`, so `validation.omitted_files: warn` + `--strict` fails on any orphan page.

## ADR Template (`docs/adr/template.md`)

```markdown
# ADR-NNNN: {Short decision title}

- **Status**: Accepted
- **Date**: YYYY-MM-DD
- **Deciders**: Sisques Labs

## Context
{Forces, constraints and the problem being decided. Link the architecture page it comes from.}

## Decision
{The decision, in the active voice: "We will ...".}

### Alternatives considered
- **{Alternative}** — rejected because {reason}.

## Consequences
- **Positive**: {what this buys}
- **Negative**: {cost accepted}
- **Follow-ups**: {deferred work, or "None"}
```

All nine ADRs use exactly these headings. `Status` values allowed: `Accepted`, `Proposed`, `Superseded by ADR-NNNN`.

## GitHub Actions Workflow (`.github/workflows/docs.yml`)

```yaml
name: docs
on:
  push:
    branches: [main]
    paths: ['docs/**', 'mkdocs.yml', 'requirements-docs.txt', '.github/workflows/docs.yml']
  pull_request:
    paths: ['docs/**', 'mkdocs.yml', 'requirements-docs.txt', '.github/workflows/docs.yml']
  workflow_dispatch:

permissions:
  contents: read

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
          cache: pip
          cache-dependency-path: requirements-docs.txt
      - run: pip install -r requirements-docs.txt
      - run: mkdocs build --strict
      - uses: actions/upload-pages-artifact@v3
        with:
          path: site

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: build
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Pull requests build but do not deploy, so `--strict` gates content before merge. `requirements-docs.txt` pins exact versions (`mkdocs==<x.y.z>`, `mkdocs-material==<x.y.z>`) resolved to the latest stable at apply time — never version ranges.

## Manual Step (not automatable)

GitHub Pages source cannot be set by code or by the workflow. A human with repo admin rights must do this **once, before the first `main` build**, or the `deploy` job fails with "Pages is not enabled":

> `https://github.com/sisques-labs/platform` → **Settings** → **Pages** → **Build and deployment** → **Source** → select **GitHub Actions** → the page saves immediately (no Save button).

This must be an explicit, non-checkable-by-code task in `tasks.md`, verified by the deploy job succeeding and `https://sisques-labs.github.io/platform/` returning the site.

## Content Migration Mapping

Source: `/Users/javi/Documents/projects/sisques-labs/sisques-account-architecture.md` (Spanish). Every target is English; names, paths, cookie names, endpoints, env vars and schema identifiers stay verbatim.

| # | Source section | Target file(s) | Notes |
|---|---|---|---|
| 1 | `## Naming` | `docs/architecture/index.md` § Naming | Platform vs. service distinction; repos and domains |
| 2 | `## Alcance y principios` | `docs/architecture/index.md` § Scope and Principles | Single-owner ecosystem, build-it-ourselves except the user store |
| 3 | `## Modelo de tenancy` | `docs/architecture/sisques-account/tenancy.md`; `docs/adr/0004-*` | Keep the "tenant, never space" terminology rule; layer 1 vs layer 2; fixed meaning of `owner` |
| 4 | `## Arquitectura del servicio` + its ASCII diagram | `docs/architecture/sisques-account/index.md` § Service architecture; `docs/diagrams/system-context.md`; `docs/adr/0001-*` | ASCII block becomes a Mermaid `flowchart`; the page links to the diagram instead of redrawing it |
| 5 | `## Proveedor de identidad` | `docs/architecture/sisques-account/index.md` § Identity provider; `docs/adr/0003-*` | Keycloak first, adapter port, explicit YAGNI on the second adapter |
| 6 | `## Modelo de sesión y tokens` | `docs/architecture/sisques-account/sessions-and-tokens.md`; `docs/diagrams/login-token-issuance.md`; `docs/diagrams/refresh-rotation.md`; `docs/adr/0002-*`, `0005-*`, `0006-*`, `0008-*` | Preserve verbatim: `access_token`, `refresh_token`, `Domain=.sisqueslabs.com`, 10–15 min TTL, `GET /.well-known/jwks.json`, `GET /api/token`, `POST /refresh`, SSR vs SPA consumption patterns |
| 7 | `## Stack` | `docs/architecture/sisques-account/index.md` § Stack | Includes the explicit rejection of `identity-service-api` (Java/Axon prototype) as a base |
| 8 | `## Modelo de datos` (schema block) | `docs/architecture/sisques-account/data-model.md`; `docs/diagrams/data-model-er.md` | Tables `app`, `user`, `tenant`, `tenant_membership`, `tenant_invite` with all columns and UNIQUE constraints; ER diagram is the Mermaid `erDiagram` twin |
| 9 | Invitation-flow paragraph (after the schema block) | `docs/architecture/sisques-account/data-model.md` § Invitation flow | Invites are offered, never auto-accepted |
| 10 | `**Bootstrap de platform_admin**` paragraph | `docs/architecture/sisques-account/data-model.md` § `platform_admin` bootstrap; `docs/adr/0007-*` | `PLATFORM_ADMIN_EMAILS`, checked on every login |
| 11 | `## Frontend` | `docs/architecture/sisques-account/index.md` § Repositories | `account-api` (NestJS), `account-web` (Next.js), `/admin` on the same session |
| 12 | `## Estado` | `docs/architecture/sisques-account/status-and-mvp.md` § Current status | Update with verified facts: `account-platform-mvp` implemented/verified/archived in `account-api`; `account-web` shipped the cross-domain SSO redirect allowlist; gardenia-api migration not started |
| 13 | `## MVP` (in and out of scope) | `docs/architecture/sisques-account/status-and-mvp.md` § MVP scope / Deferred | Keep "deferred, not rejected" wording for `account-web` and email invites |
| 14 | *(not in source — from exploration)* gardenia-api JWT/tenancy conflict | `docs/open-questions.md` | Locked gardenia-api rule "tenant must never be embedded in a JWT" vs. Sisques Account's JWT claims model. Explicitly **unresolved**; must not be decided in this change |
| 15 | *(new)* Repo map + platform overview | `README.md`; `docs/index.md` | README owns the canonical repo map (D7); `docs/index.md` is the site home with entry links |

## File Changes

| File | Action | Description |
|---|---|---|
| `README.md` | Create | Overview + canonical repo map (Gardenia, Nexora, Sisques Account / account-api / account-web, Portero) + link to the site |
| `mkdocs.yml` | Create | Config above |
| `requirements-docs.txt` | Create | Pinned `mkdocs`, `mkdocs-material` |
| `.github/workflows/docs.yml` | Create | Build + deploy above |
| `docs/index.md` | Create | Site home |
| `docs/architecture/index.md` | Create | Naming, scope, principles |
| `docs/architecture/sisques-account/{index,tenancy,sessions-and-tokens,data-model,status-and-mvp}.md` | Create (5) | Migrated architecture |
| `docs/diagrams/{index,system-context,login-token-issuance,refresh-rotation,data-model-er}.md` | Create (5) | Mermaid diagram pages |
| `docs/adr/index.md`, `docs/adr/template.md`, `docs/adr/000{1..9}-*.md` | Create (11) | ADR set + index + template |
| `docs/open-questions.md` | Create | gardenia-api conflict, marked unresolved |

28 new files, 0 modified, 0 deleted. Repository settings change is manual (above).

## Testing Strategy

Documentation-only: no unit, integration, or E2E tests. Verification is structural.

| Layer | What to verify | Approach |
|---|---|---|
| Build | Site builds clean | `mkdocs build --strict` (warnings become errors) |
| Links & nav | No broken internal links, anchors, or orphan pages | MkDocs `validation:` block above, enforced by `--strict` |
| Diagrams | Every Mermaid fence renders | Local `mkdocs serve` visual check of the 4 diagram pages |
| Content fidelity | No decided fact lost or altered in translation | Walk rows 1–15 of the migration table against the source doc |
| Deployment | Site is live | `deploy` job green and `https://sisques-labs.github.io/platform/` reachable |

## Threat Matrix

| Boundary | Applicability | Design response | Planned check |
|---|---|---|---|
| Documentation-like paths | **Applicable** — `requirements-docs.txt` and `.github/workflows/docs.yml` are active content inside a change labelled "docs-only" | These two files are not passive markdown: they install packages and hold `pages: write` / `id-token: write`. Pinned versions, least-privilege `permissions`, deploy gated on `github.ref == 'refs/heads/main'` | Review both files explicitly rather than classifying the whole change as passive |
| Git repository selection | N/A — the change authors no `git -C` or path-selecting command | — | — |
| Commit state | N/A — no index/worktree automation authored | — | — |
| Push state | N/A — deploy uploads an artifact; nothing is pushed to a branch (see D5) | — | — |
| PR commands | N/A — no PR automation authored | — | — |

## Migration / Rollout

Single additive commit set; no data migration, no feature flags. Order matters once: set the Pages source **before** the first push to `main` touching `docs/`, otherwise the first `deploy` job fails. Rollback: revert the commits and set Pages source back to **None**.

## Open Questions

- [x] Site URL assumes `https://sisques-labs.github.io/platform/`. Confirmed: the real GitHub org is `sisques-labs` (verified via `gh repo list sisques-labs`, which lists `platform`, `account-api`, `account-web`, etc. as top-level repos). The source doc's `sisqueslabs/...` notation was informal prose, not an actual repo path — `site_url`/`repo_url` as written are correct.
- [ ] Exact pinned `mkdocs` / `mkdocs-material` versions are resolved at apply time, not fixed here.
