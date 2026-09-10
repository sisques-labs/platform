# Architecture Decision Records

Each locked Sisques Account decision is recorded as one ADR, using the
[MADR-lite template](template.md): context, decision, alternatives
considered, and consequences. ADRs are numbered sequentially
(`NNNN-kebab-title.md`) and never renumbered; a superseded decision is marked
`Superseded by ADR-NNNN` rather than deleted.

## Index

The ADR set is authored incrementally; this table is populated with real
links as each ADR lands.

| ADR | Decision |
|---|---|
| ADR-0001 | Sisques Account owns identity |
| ADR-0002 | Account issues its own JWT |
| ADR-0003 | IdP behind a swappable adapter |
| ADR-0004 | Two-layer tenancy model |
| ADR-0005 | JWKS endpoint for key distribution |
| ADR-0006 | Refresh rotation with reuse detection |
| ADR-0007 | `platform_admin` bootstrap via env |
| ADR-0008 | Cookies scoped to `.sisqueslabs.com` |
| ADR-0009 | MkDocs + Material for the docs site |
