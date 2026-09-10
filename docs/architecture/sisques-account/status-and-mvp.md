# Status & MVP

## Current status

- Repos created and cloned: `sisques-labs/account-api` (from
  `nestjs-template`) and `sisques-labs/account-web` (from
  `nextjs-template`), public, at `account/account-api` and
  `account/account-web`.
- `account-platform-mvp` is implemented, verified, and archived in
  `account-api`.
- `account-web` shipped the cross-domain SSO redirect allowlist.
- Migration of `gardenia-api` to delegate its auth/tenancy to Sisques
  Account has not started. See [Open Questions](../../open-questions.md)
  for the gardenia-api JWT conflict this migration will need to resolve.

## MVP scope

Scope: **`account-api` alone, validated with Postman/tests — without
`account-web` and without email invitations yet.** Goal: prove the core of
the platform (identity + tenancy layer 1) in isolation, without touching
Gardenia or any other app — the migration of Gardenia (which today has its
own working `contexts/auth` + tenant-repository in `gardenia-api`, with its
own JWT + refresh + OAuth) is planned separately, later.

In scope for the MVP:

- Keycloak adapter (register/login) issuing Sisques Account's own JWT +
  refresh token, per the session model described in
  [Sessions & Tokens](sessions-and-tokens.md).
- `POST /tenants` — creates a tenant; the creator automatically becomes its
  `owner`.
- Adding an existing user as a tenant member directly (without an email
  invitation).
- Refresh endpoint, with rotation + reuse detection (the `gardenia-api`
  pattern).
- JWKS endpoint (`GET /.well-known/jwks.json`) exposing the signing public
  key.
- `platform_admin` bootstrap via `PLATFORM_ADMIN_EMAILS`.

## Deferred

Out of the MVP (deferred, not rejected):

- **`account-web`** — login/registration in Next.js. Built once there is a
  real app that needs it.
- **Email invitations** (`tenant_invite` with real delivery) — requires
  adding an email provider (SMTP/Resend/SendGrid...) as a new dependency.
  Until then, `tenant_membership` can be created directly.
- Migration of `gardenia-api` to delegate its current auth/tenancy to
  Sisques Account.
