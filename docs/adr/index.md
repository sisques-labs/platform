# Architecture Decision Records

Each locked Sisques Account decision is recorded as one ADR, using the
[MADR-lite template](template.md): context, decision, alternatives
considered, and consequences. ADRs are numbered sequentially
(`NNNN-kebab-title.md`) and never renumbered; a superseded decision is marked
`Superseded by ADR-NNNN` rather than deleted.

## Index

| ADR | Decision |
|---|---|
| [ADR-0001](0001-sisques-account-owns-identity.md) | Sisques Account owns identity |
| [ADR-0002](0002-account-issues-its-own-jwt.md) | Account issues its own JWT |
| [ADR-0003](0003-idp-behind-swappable-adapter.md) | IdP behind a swappable adapter |
| [ADR-0004](0004-two-layer-tenancy-model.md) | Two-layer tenancy model |
| [ADR-0005](0005-jwks-endpoint-key-distribution.md) | JWKS endpoint for key distribution |
| [ADR-0006](0006-refresh-rotation-reuse-detection.md) | Refresh rotation with reuse detection |
| [ADR-0007](0007-platform-admin-bootstrap-via-env.md) | `platform_admin` bootstrap via env |
| [ADR-0008](0008-cookies-scoped-to-parent-domain.md) | Cookies scoped to `.sisqueslabs.com` |
| [ADR-0009](0009-mkdocs-material-docs-site.md) | MkDocs + Material for the docs site |
