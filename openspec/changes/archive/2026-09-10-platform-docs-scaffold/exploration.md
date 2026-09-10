# Exploration: platform-docs-scaffold

## Current State

- Repo `sisques-labs/platform`: single commit (`chore: initial commit`), only `.gitkeep`. No app code, no toolchain. Public repo under the `sisques-labs` GitHub org.
- `openspec/config.yaml` already declares the intended shape (`README.md`, `docs/architecture/`, `docs/adr/`, `docs/diagrams/`, GitHub Pages leaning MkDocs+Material) and notes verification will be build-based once the scaffold lands.
- `openspec/specs/` and `openspec/changes/archive/` are empty placeholders; this change creates the first change folder.
- Source content lives outside the repo: `/Users/javi/Documents/projects/sisques-labs/sisques-account-architecture.md`, a finalized Spanish-language design doc for **Sisques Account** (the shared identity/tenancy service), now superseded by this repo and needing migration (translation + restructuring, not copy-paste).
- No GitHub Pages, CI workflows, or docs tooling configured anywhere yet.

## Affected Areas

Everything below is a new creation — nothing pre-exists:

- `README.md` — platform overview + map of apps/repos (Gardenia, Nexora, Sisques Account/account-api/account-web, Portero, etc.)
- `docs/architecture/` — first content: Sisques Account architecture page(s)
- `docs/adr/` — candidate ADRs listed below
- `docs/diagrams/` — Mermaid diagrams (embedded vs. standalone `.mmd` files is a design-phase call)
- `mkdocs.yml` (or equivalent) — site config, if MkDocs is chosen
- `.github/workflows/*.yml` — CI workflow building and deploying to Pages
- GitHub repo Settings → Pages source — a manual, one-time repo setting (not code); must not be silently skipped in tasks
- `openspec/changes/platform-docs-scaffold/` — this SDD change folder itself

## Approaches

### 1. MkDocs + Material, deployed via GitHub Actions to Pages (recommended default)

- Pros: native Mermaid rendering (`pymdownx.superfences`), real nav/search, `docs/` is the single source for both repo browsing and the generated site (no duplication), simple Python-only CI toolchain
- Cons: adds a toolchain dependency the repo doesn't have today; requires learning `mkdocs.yml` nav conventions; requires a Pages-source repo setting change
- Effort: Low-Medium

### 2. Bare Jekyll (GitHub Pages native)

- Pros: zero-config, builds automatically without a custom Action
- Cons: no native Mermaid rendering (fragile JS-injection workarounds), weak nav/search without heavy plugin work, front-matter conventions leak into every file
- Effort: Low upfront, Medium-High to reach parity on Mermaid+nav

### 3. Docusaurus (React-based)

- Pros: excellent nav/search/versioning, native Mermaid plugin, polished theme
- Cons: Node/React toolchain is heavier than needed for a single-owner docs repo; MDX diverges from plain markdown `docs/`
- Effort: Medium-High

### 4. No generated site — raw markdown on GitHub

- Pros: zero tooling/CI
- Cons: explicitly rejected — the user wants a real website, not raw markdown; no reliable nav/search
- Effort: None, but fails the stated requirement

## Recommendation

MkDocs + Material via GitHub Actions to Pages, with `docs/` as the single source for both the repo and the generated site — matches `openspec/config.yaml`'s already-recorded intent. **Not yet formally locked**: `sdd-propose` should present it as the default alongside the Jekyll/Docusaurus alternatives so the user can explicitly confirm or override.

## Candidate ADRs (to formalize in sdd-spec, not resolved here)

1. Sisques Account owns identity — apps never talk to the IdP (Keycloak) directly
2. Sisques Account issues its own JWT (short access + opaque refresh), decoupling apps from the underlying IdP
3. IdP sits behind a swappable adapter, starting with Keycloak (YAGNI on the second adapter)
4. Two-layer tenancy model — platform owns membership (layer 1), each app owns in-app role meaning (layer 2)
5. Public key distribution via JWKS endpoint instead of a fixed env var (enables rotation without app redeploys)
6. Refresh token rotation + reuse detection reuses gardenia-api's proven production pattern
7. `platform_admin` bootstrap via `PLATFORM_ADMIN_EMAILS` env var, checked on every login
8. Session cookies scoped to `.sisqueslabs.com` for cross-app SSO without a redirect flow
9. (Candidate, possibly proposal-only rather than a formal ADR) MkDocs + Material chosen as the docs site generator

## Content Migration Notes

- Source doc is Spanish; platform docs must be authored in English per the artifact-language convention — a translation + restructuring task, not a copy.
- Decided facts (data model, stack, cookie names, endpoint paths, env var names) must be preserved exactly in meaning.
- The gardenia-api migration conflict (locked decision: tenant/space must never be embedded in a JWT, vs. Sisques Account's JWT-based model) must appear as a documented "Open Questions / Future Work" item, not silently resolved or omitted.
- Implementation status to record accurately: `account-platform-mvp` is implemented/verified/archived in `account-api`; `account-web` shipped cross-domain SSO redirect allowlist; gardenia-api migration not started.

## Risks

- No Python toolchain exists yet — first-time CI setup needed for `mkdocs`/`mkdocs-material`.
- GitHub Pages "Actions" source is a manual repo Settings change, easy to silently skip in tasks.
- Translation/restructuring risk: could drift from the "already final" decided facts if not cross-checked carefully.
- Scope risk: the account-api/gardenia-api JWT conflict must stay marked deferred, not accidentally resolved by omission.
- Verification methodology: this is docs-only, so `sdd-verify` needs a structural plan (`mkdocs build --strict`, link checker, Mermaid syntax check) rather than functional tests.

## Ready for Proposal

Yes. Structure, tooling default, and first content migration are well understood; no open design question blocks `sdd-propose`. The gardenia-api conflict is explicitly out of scope for this change.
