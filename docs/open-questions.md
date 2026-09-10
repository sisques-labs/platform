# Open Questions

## gardenia-api JWT / tenancy conflict

**Status: Unresolved.**

Sisques Account's session model issues its own JWT (`access_token`) whose
claims carry identity, tenants, and roles together — see
[Sessions & Tokens](architecture/sisques-account/sessions-and-tokens.md).

`gardenia-api` has a separately locked rule from its own production
auth/tenancy implementation: **tenant must never be embedded in a JWT.**

These two decisions conflict, and this page does not resolve the conflict.
It will need to be addressed explicitly before `gardenia-api`'s migration
to Sisques Account begins — see
[Status & MVP](architecture/sisques-account/status-and-mvp.md), which
records that this migration has not started.

This page intentionally does **not** propose or imply a resolution. It
exists so the conflict is recorded rather than silently decided by
omission.
