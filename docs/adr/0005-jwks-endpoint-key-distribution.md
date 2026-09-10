# ADR-0005: JWKS endpoint for key distribution

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Apps with their own backend (SSR) need to validate the `access_token`
signature locally, without calling Sisques Account on every request. A
fixed public key distributed via an environment variable would require
manually reconfiguring and redeploying every app on each key rotation. See
[Sessions & Tokens](../architecture/sisques-account/sessions-and-tokens.md).

## Decision

We will publish Sisques Account's signing public key(s) via a standard JWKS
endpoint (`GET /.well-known/jwks.json`). Apps cache the key(s) and refresh
them, so the signing key can be rotated without touching or redeploying any
app.

### Alternatives considered

- **Fixed public key via environment variable** — rejected because
  rotation requires manually reconfiguring and redeploying every app.

## Consequences

- **Positive**: key rotation needs zero app redeploys.
- **Negative**: apps must implement correct key caching/refresh logic.
- **Follow-ups**: None.
