# Sisques Labs Platform

Sisques Labs Platform is the shared identity and tenancy layer behind the
Sisques Labs ecosystem — Gardenia, Nexora, and future apps. This site is the
canonical, versioned home for platform-wide architecture decisions that used
to live in an unversioned document outside any repository.

## What's here

This site is being built out section by section. Once complete, it covers:

- [**Architecture**](architecture/index.md) — the Sisques Account service
  design: identity, tenancy, sessions and tokens, data model, and current
  implementation status.
- [**Diagrams**](diagrams/index.md) — [system context](diagrams/system-context.md),
  [token issuance](diagrams/login-token-issuance.md),
  [refresh rotation](diagrams/refresh-rotation.md), and
  [data model](diagrams/data-model-er.md) diagrams.
- [**Decisions (ADR)**](adr/index.md) — all 9 locked architecture decision
  records for Sisques Account.
- [**Open Questions**](open-questions.md) — cross-app conflicts that are
  deliberately left unresolved rather than silently decided.

For the full repository map (every app and service in the ecosystem), see the
[repository README](https://github.com/sisques-labs/platform#readme).
