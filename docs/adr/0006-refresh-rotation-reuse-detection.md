# ADR-0006: Refresh rotation with reuse detection

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

The platform needs a revocation-aware, replay-resistant `refresh_token`
mechanism. A proven pattern already exists in production in `gardenia-api`,
with real bugfixes — including a race condition — already resolved. See
[Refresh Rotation](../diagrams/refresh-rotation.md).

## Decision

We will reuse `gardenia-api`'s refresh pattern: rotate the `refresh_token`
on every use with a pessimistic lock, and detect reuse — using an
already-consumed `refresh_token` invalidates the entire session chain, a
signal of token theft. No new mechanism is designed for the platform
without a concrete need that justifies it.

### Alternatives considered

- **Design a new refresh mechanism for the platform** — rejected; no
  concrete need justifies it, and it would forgo `gardenia-api`'s
  already-resolved production bugfixes.

## Consequences

- **Positive**: inherits a production-proven implementation with its race
  condition already fixed.
- **Negative**: requires a pessimistic lock on every refresh, adding
  contention under concurrent refresh attempts.
- **Follow-ups**: None.
