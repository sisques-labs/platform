# Proposal: Platform Documentation Scaffold

## Intent

The `platform` repo is empty while finalized cross-app decisions live in an unversioned Spanish document outside any repo. Create the documentation home — structure, first content, published site — so platform decisions are discoverable and citable by every app repo.

Success: a GitHub Pages site sourced solely from `docs/`, carrying the Sisques Account architecture and its ADRs in English.

## Scope

### In Scope

- `README.md`: platform overview + repo map (Gardenia, Nexora, Sisques Account / account-api / account-web, Portero).
- Scaffold `docs/architecture/`, `docs/adr/`, `docs/diagrams/`.
- Migrate `sisques-account-architecture.md` (Spanish) into English architecture pages, preserving decided facts exactly in meaning.
- ~8–9 ADRs: shared identity service, JWT + JWKS, two-layer tenancy, Keycloak behind adapter, refresh rotation + reuse detection, `platform_admin` bootstrap, `.sisqueslabs.com` cookie scoping.
- MkDocs + Material config and a GitHub Actions build/deploy workflow.
- An explicit task for the manual Settings → Pages source change.
- Open Questions page recording the gardenia-api JWT-embedding conflict as unresolved.

### Out of Scope

- Resolving the gardenia-api vs. Sisques Account JWT/tenancy conflict.
- Any application code or runtime change in any repo.
- Gardenia/Nexora/Portero architecture beyond the README map.
- Versioned docs, i18n, custom domain, second IdP adapter.

## Capabilities

### New Capabilities

- `platform-docs-structure`: README, `docs/` layout, navigation contract.
- `account-architecture-docs`: migrated architecture pages, ADR set, open-questions record.
- `docs-site-publishing`: MkDocs config, Actions workflow, Pages deployment.

### Modified Capabilities

- None (`openspec/specs/` is empty; first change).

## Approach

MkDocs + Material deployed by GitHub Actions, with `docs/` as the single source for repo and site — no duplication, native Mermaid via `pymdownx.superfences`, real nav/search. Matches intent already recorded in `openspec/config.yaml`.

Rationale: Jekyll is zero-config but lacks native Mermaid and usable nav/search; Docusaurus has the best nav but imposes a Node/React/MDX toolchain heavier than a single-owner docs repo needs. Both stay viable — MkDocs is stated as the default so the choice is explicit and overridable in review.

Migration is translation + restructuring, not copy-paste: prose becomes architecture pages, each locked decision becomes one ADR, and implementation status is recorded accurately (`account-platform-mvp` archived in account-api; cross-domain SSO allowlist shipped in account-web; gardenia-api migration not started).

## Affected Areas

| Area | Impact | Description |
|------|--------|-------------|
| `README.md` | New | Overview + repo map |
| `docs/architecture/` | New | Sisques Account pages |
| `docs/adr/` | New | ~8–9 ADRs |
| `docs/diagrams/` | New | Mermaid diagrams |
| `mkdocs.yml` | New | Site config and nav |
| `.github/workflows/` | New | Build/deploy to Pages |
| Repo Settings | Manual | Pages source = "GitHub Actions" |

## Risks

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| Translation drifts from decided facts | Med | Cross-check each page; keep names, paths, env vars verbatim |
| Pages source setting silently skipped | Med | Explicit non-automatable task in `tasks.md` |
| No Python toolchain yet | Low | Pin `mkdocs`/`mkdocs-material` versions in workflow |
| Conflict "resolved" by omission | Low | Open Questions page is a required deliverable |

## Rollback Plan

Additive and docs-only: revert the change's commits (all new files) and set Pages source back to "None". No application, database, or runtime state is touched.

## Dependencies

- Read access to `/Users/javi/Documents/projects/sisques-labs/sisques-account-architecture.md`.
- Repo admin rights to set the Pages source (manual, one-time).

## Verification Note

Documentation-only: `sdd-spec` and `sdd-tasks` MUST NOT introduce functional or unit-test requirements. Verification is structural — `mkdocs build --strict` (or equivalent), a markdown link checker, Mermaid syntax validity.

## Success Criteria

- [ ] Site builds and deploys from `docs/` with no duplicated content.
- [ ] `mkdocs build --strict` passes with zero warnings; no broken internal links.
- [ ] README maps every known app/repo.
- [ ] Every locked Sisques Account decision has a corresponding ADR.
- [ ] The gardenia-api JWT conflict is present and explicitly marked unresolved.
