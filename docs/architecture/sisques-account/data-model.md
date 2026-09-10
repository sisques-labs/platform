# Data Model

```
app
├─ id
├─ slug           (e.g. "gardenia", "nexora")
├─ name
└─ created_at

user
├─ id
├─ external_id     (Keycloak's sub, unique)
├─ email
├─ display_name
├─ platform_admin  (bool, default false — access to /admin in account-web)
└─ created_at

tenant
├─ id
├─ app_id          (FK -> app)
├─ name
├─ slug
├─ created_at
└─ UNIQUE(app_id, slug)   -- slug is unique per app, not global

tenant_membership
├─ id
├─ tenant_id       (FK -> tenant)
├─ user_id         (FK -> user)
├─ role            (free text; "owner" has a fixed meaning, see Tenancy Model)
├─ created_at
└─ UNIQUE(tenant_id, user_id)

tenant_invite
├─ id
├─ tenant_id       (FK -> tenant)
├─ email           (does not require a user_id — the user may not exist yet)
├─ role            (role granted on acceptance)
├─ invited_by      (FK -> user)
├─ token           (unique, goes in the email link)
├─ status          (pending | accepted | revoked | expired)
├─ expires_at
└─ created_at
```

The [Data Model (ER) diagram](../../diagrams/data-model-er.md) is the
entity-relationship twin of this schema.

## Invitation flow

Someone without an account yet can be invited by email. On login/signup,
Sisques Account looks for a `tenant_invite` with a matching `email` and
`status=pending`, and offers it to the user to accept explicitly (invites
are never auto-accepted).

## `platform_admin` bootstrap

Resolved by configuration, not by a manual command. The
`PLATFORM_ADMIN_EMAILS` environment variable (comma-separated list) is
checked on every login; if the authenticated user's email matches, their
`user` row is automatically marked `platform_admin=true`. Reproducible in
any environment (local/staging/prod) without touching the database by
hand. This decision is recorded as ADR-0007.
