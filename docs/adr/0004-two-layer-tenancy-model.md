# ADR-0004: Two-layer tenancy model

- **Status**: Accepted
- **Date**: 2026-09-10
- **Deciders**: Sisques Labs

## Context

Every app in the ecosystem needs multi-tenancy, but each app's notion of a
role differs (e.g. in Gardenia a "member" can water but not delete plants).
See [Tenancy Model](../architecture/sisques-account/tenancy.md).

## Decision

We will split tenancy into two layers. Layer 1 (membership mechanics: name,
owners, members, pending invitations, a generic role label per member)
lives in the platform and is identical across every app. Layer 2 (what each
role means inside a given app) is owned entirely by that app; the platform
stores role labels and returns them in the token without interpreting them.
The platform-level concept is fixed as **tenant** — never "space" — to
avoid confusing it with any app's own product terminology.

### Alternatives considered

- **Let the platform also own role-meaning semantics** — rejected, would
  force every app into the same in-app authorization model.
- **Use app-specific terminology (e.g. "space") in the platform schema** —
  rejected as ambiguous once more apps join the ecosystem.

## Consequences

- **Positive**: apps keep full control of their own in-app authorization.
- **Negative**: the platform cannot enforce any invariant on non-`owner`
  roles; `owner` is the only role with a fixed platform-level meaning.
- **Follow-ups**: None.
