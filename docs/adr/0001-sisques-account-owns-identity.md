# ADR-0001: Sisques Account owns identity

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Every app in the Sisques Labs ecosystem needs to authenticate users. Without
one shared identity service, each app would talk to the identity provider
directly, coupling every app to that specific provider. See
[Sisques Account overview](../architecture/sisques-account/index.md).

## Decision

We will centralize identity in Sisques Account. Apps never talk directly to
the identity provider (Keycloak/Cognito/etc.); they only talk to Sisques
Account, which issues its own signed tokens carrying its own claims
(identity + tenants + roles).

### Alternatives considered

- **Each app talks to the identity provider directly** — rejected because
  it couples every app to that provider and blocks swapping providers
  without touching every app.

## Consequences

- **Positive**: the identity provider can be swapped without touching any
  app.
- **Negative**: Sisques Account becomes a single point of dependency for
  auth across all apps.
- **Follow-ups**: None.
