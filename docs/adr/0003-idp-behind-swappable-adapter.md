# ADR-0003: IdP behind a swappable adapter

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Building password hashing, MFA, email verification, password recovery and
brute-force protection by hand has a real security cost, not just a time
cost. At the same time, locking into one cloud-specific identity provider
forever is a long-term risk. See
[Identity provider](../architecture/sisques-account/index.md#identity-provider).

## Decision

We will delegate the user store to an external identity provider behind a
configurable port/adapter (environment variable), starting with Keycloak
(open-source, self-hosted, not tied to a specific cloud). A second adapter
(e.g. Cognito) is not built yet — only the extension point is kept clean
(YAGNI).

### Alternatives considered

- **Build password/MFA/user-store management in-house** — rejected for its
  real security cost.
- **Build multiple identity-provider adapters now** — rejected as YAGNI;
  no second adapter is needed yet.

## Consequences

- **Positive**: avoids long-term lock-in to one identity provider in
  production.
- **Negative**: adds Keycloak as an operational dependency today.
- **Follow-ups**: build a second adapter (e.g. Cognito) when a concrete
  need appears.
