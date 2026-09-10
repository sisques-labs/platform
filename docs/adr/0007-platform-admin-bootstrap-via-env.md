# ADR-0007: `platform_admin` bootstrap via env

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Platform-admin access to `/admin` in `account-web` needs a reproducible way
to grant the `platform_admin` flag across environments (local/staging/prod)
without touching the database by hand. See
[Data Model](../architecture/sisques-account/data-model.md#platform_admin-bootstrap).

## Decision

We will resolve `platform_admin` bootstrap by configuration: the
`PLATFORM_ADMIN_EMAILS` environment variable (comma-separated list) is
checked on every login; a matching authenticated user's `user` row is
automatically marked `platform_admin=true`.

### Alternatives considered

- **Manual database command/migration to set the flag** — rejected; not
  reproducible across environments without manual database access.

## Consequences

- **Positive**: reproducible in any environment without manual database
  access.
- **Negative**: anyone who controls the `PLATFORM_ADMIN_EMAILS` environment
  variable can grant platform-admin access.
- **Follow-ups**: None.
