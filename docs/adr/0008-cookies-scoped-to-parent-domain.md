# ADR-0008: Cookies scoped to `.sisqueslabs.com`

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

All apps in the ecosystem live under subdomains of `sisqueslabs.com`.
Propagating a session across apps could use a full OAuth redirect flow
between apps, but that is heavier than needed when every app shares one
parent domain. See
[Sessions & Tokens](../architecture/sisques-account/sessions-and-tokens.md).

## Decision

We will scope both session cookies (`access_token`, `refresh_token`) to
`Domain=.sisqueslabs.com`, both `httpOnly`, so a session is shared across
all apps under the domain with no inter-app redirect flow.  `access_token`
is `httpOnly` (shielded against XSS) even though this requires the extra
`GET login.sisqueslabs.com/api/token` step for SPA apps that cannot read an
`httpOnly` cookie directly.

### Alternatives considered

- **Full OAuth redirect flow between apps to propagate session** —
  rejected as unnecessary overhead given the shared parent domain.
- **`access_token` readable by JS (not `httpOnly`)** — rejected for XSS
  exposure.

## Consequences

- **Positive**: cross-app SSO with no redirect flow.
- **Negative**: ties the whole ecosystem to subdomains of one parent
  domain.
- **Follow-ups**: None.
