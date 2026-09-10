# Tenancy Model

**Terminology:** the platform-level concept is called a **tenant**. "Space"
is only the name Gardenia gives to the same concept in its own domain/UI —
do not use "space" for anything in the platform or in the schema, to avoid
confusing it with a specific app's product name.

Two layers are distinguished within "multi-tenant", and only the first
lives in the platform:

1. **Membership mechanics (layer 1, in the platform):** a tenant has a
   name, one or more owners, members, pending invitations, and a role per
   member (generic label: owner/admin/member...). The same across every
   app.
2. **Domain authorization (layer 2, in each app):** what each role means
   inside that specific app (e.g. in Gardenia a "member" can water but not
   delete plants). Each app defines and controls this; the platform has no
   opinion on it.

Concrete example that originated this distinction: Gardenia has N "spaces"
(= tenants) per user; the platform knows they exist and who belongs to each
one, but does not know what each role can do inside a Gardenia tenant.

Each tenant always belongs to a single app (there are no tenants shared
across apps) — which is why `tenant` carries `app_id` in the schema (see
[Data Model](data-model.md)).

`owner` is the only role with a fixed meaning for the platform: only an
owner can invite/remove members, or rename or delete the tenant. Every
other role is a free-form label that each app defines and interprets
(layer 2); the platform stores it and returns it in the token, without
giving it meaning. Every tenant must have at least one owner (a business
rule enforced in the service layer, not expressible as a DB constraint with
free-form roles).

This split (platform-owned membership vs. app-owned role meaning) is
recorded as ADR-0004: Two-layer tenancy model.
