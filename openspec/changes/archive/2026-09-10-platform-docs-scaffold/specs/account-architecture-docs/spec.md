# Account Architecture Docs Specification

## Purpose

Captures the migrated Sisques Account architecture — translated from the source Spanish document into English pages — its locked decisions as ADRs, and the explicitly unresolved gardenia-api JWT conflict.

## Requirements

### Requirement: Migrated Architecture Pages

`docs/architecture/` MUST contain English-language pages describing the Sisques Account architecture, translated and restructured from `sisques-account-architecture.md`, preserving every decided fact (names, paths, env vars) verbatim in meaning.

#### Scenario: Architecture pages present in English

- GIVEN `docs/architecture/`
- WHEN its pages are read
- THEN all content is in English
- AND no page is a raw untranslated copy of the source document

#### Scenario: Implementation status recorded accurately

- GIVEN the architecture pages
- WHEN implementation status is described
- THEN `account-platform-mvp` is noted as archived in account-api
- AND cross-domain SSO allowlist is noted as shipped in account-web
- AND gardenia-api migration is noted as not started

### Requirement: ADR Coverage for Locked Decisions

`docs/adr/` MUST contain one ADR per locked Sisques Account decision, covering at minimum: shared identity service, JWT + JWKS, two-layer tenancy, Keycloak behind an adapter, refresh token rotation with reuse detection, `platform_admin` bootstrap via `PLATFORM_ADMIN_EMAILS`, and `.sisqueslabs.com` cookie scoping.

#### Scenario: Every locked decision has an ADR

- GIVEN `docs/adr/`
- WHEN the ADR set is enumerated
- THEN an ADR exists for the shared identity service
- AND an ADR exists for JWT + JWKS
- AND an ADR exists for two-layer tenancy
- AND an ADR exists for Keycloak behind an adapter
- AND an ADR exists for refresh rotation with reuse detection
- AND an ADR exists for `platform_admin` bootstrap via `PLATFORM_ADMIN_EMAILS`
- AND an ADR exists for `.sisqueslabs.com` cookie scoping

#### Scenario: ADRs follow a consistent record format

- GIVEN any file under `docs/adr/`
- WHEN it is opened
- THEN it states context, decision, and consequences

### Requirement: Open Questions Page Records the Gardenia JWT Conflict

`docs/architecture/` MUST include an Open Questions page, and that page MUST record the gardenia-api JWT-embedding conflict with Sisques Account's tenancy model as explicitly unresolved; the page MUST NOT present a resolution or a preferred outcome.

#### Scenario: Gardenia-api JWT conflict is present and marked unresolved

- GIVEN the Open Questions page
- WHEN it is read
- THEN it describes the gardenia-api JWT-embedding conflict with the Sisques Account tenancy model
- AND it is labeled as an open, unresolved question
- AND no resolution or decision is stated for it
