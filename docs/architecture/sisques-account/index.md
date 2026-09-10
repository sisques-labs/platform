# Sisques Account — Overview

Sisques Account is the shared identity and tenancy service for the Sisques
Labs Platform.

## Service architecture

- Sisques Account is a **shared service with its own database** — the
  runtime source of truth, not a library/package that each app installs
  with its own copy of the data. This was chosen so tenants ("spaces") for
  every app can be administered and queried from a single place.
- **Apps never talk directly to the identity provider** (Keycloak/Cognito/
  etc.) — only to Sisques Account. Sisques Account issues its own tokens,
  signed with its own key, carrying its own claims (identity + tenants +
  roles). This is what allows switching identity providers without
  touching any app.

The System Context diagram shows this same flow: users authenticate against
Sisques Account, which talks OIDC internally to the active identity-provider
adapter (Keycloak today), then issues its own token for Gardenia, Nexora and
other apps — which never talk to the identity provider directly.

## Identity provider

- The **user store** (passwords, hashing, MFA, email verification, password
  recovery, brute-force protection) is delegated to an external provider
  instead of being built by hand — this is the piece where reinventing the
  wheel has a real security cost, not just a time cost.
- It is implemented behind a configurable **port/adapter** (environment
  variable), so the platform does not depend on the chosen provider
  forever:
  - **We start with Keycloak** (open-source, self-hosted, not tied to a
    specific cloud).
  - Designed with the mindset of being able to add another adapter
    (Cognito or similar) later without rewriting the platform — but the
    second adapter is **not** built yet (YAGNI); only the extension point
    is kept clean.
- Explicit motivation: this is not only about "local vs. production", but
  about avoiding long-term lock-in to one provider in production.

## Stack

- **NestJS/TypeScript**, to fit the rest of the ecosystem (`gardenia-api`,
  `nexora-api`, and `local-dev-stack`, which is explicitly designed for
  services cloned from `sisqueslabs/nestjs-template`).
- The existing `identity-service-api` repo (Java 21 + Spring Boot + Axon
  Framework, event-sourced, 100% custom auth without Keycloak) is rejected
  as a base — it was a prior prototype/learning exercise, not the reference
  implementation. Its approach (and its stack) is not reused when building
  `sisqueslabs/account`.

## Repositories

- `sisqueslabs/account-api` (NestJS) — the backend: Keycloak adapter,
  tenancy/"spaces" logic, token issuance and validation, admin endpoints.
- `sisqueslabs/account-web` (Next.js, cloned from
  `templates/nextjs-template`) — a single frontend serving both the public
  login/registration pages and, after the same login, an `/admin` section
  visible only to the platform administrator role. Living on the same
  domain and reusing the same session cookie already designed, no
  additional SSO mechanism is needed between login and admin — it is the
  same app, the same session.
