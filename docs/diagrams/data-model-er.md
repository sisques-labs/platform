# Data Model (ER)

Entity-relationship twin of the [Data Model](../architecture/sisques-account/data-model.md)
schema.

```mermaid
erDiagram
    APP ||--o{ TENANT : has
    USER ||--o{ TENANT_MEMBERSHIP : has
    TENANT ||--o{ TENANT_MEMBERSHIP : has
    TENANT ||--o{ TENANT_INVITE : has
    USER ||--o{ TENANT_INVITE : invites

    APP {
        id id
        string slug
        string name
        datetime created_at
    }
    USER {
        id id
        string external_id
        string email
        string display_name
        boolean platform_admin
        datetime created_at
    }
    TENANT {
        id id
        id app_id
        string name
        string slug
        datetime created_at
    }
    TENANT_MEMBERSHIP {
        id id
        id tenant_id
        id user_id
        string role
        datetime created_at
    }
    TENANT_INVITE {
        id id
        id tenant_id
        string email
        string role
        id invited_by
        string token
        string status
        datetime expires_at
        datetime created_at
    }
```

`tenant.slug` is unique per app (`UNIQUE(app_id, slug)`), not global.
`tenant_membership` is unique per `(tenant_id, user_id)`.
