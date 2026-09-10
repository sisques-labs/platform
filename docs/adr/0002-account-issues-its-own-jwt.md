# ADR-0002: Account issues its own JWT

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Apps need a way to trust an authenticated identity without depending on the
identity provider's own token format or on the platform being reachable on
every request. See [Sessions & Tokens](../architecture/sisques-account/sessions-and-tokens.md).

## Decision

We will have Sisques Account issue its own short-lived JWT (`access_token`,
10–15 min) plus an opaque, long-lived `refresh_token`, both `httpOnly`
cookies scoped to `Domain=.sisqueslabs.com`, instead of forwarding the
identity provider's token or requiring a live call to the platform on every
request.

### Alternatives considered

- **Forward the identity provider's own token to apps** — rejected because
  it couples apps to that provider's token format.
- **Live call to the platform on every request** — rejected for the
  latency and availability cost.
- **Long-lived tokens only, no refresh** — rejected because access
  revocation would not be reflected in a reasonable time.

## Consequences

- **Positive**: apps validate tokens locally via JWKS without depending on
  Sisques Account's uptime on every request.
- **Negative**: revocation takes up to the access-token TTL to fully
  propagate.
- **Follow-ups**: None.
