# Diagrams

Standalone Mermaid diagrams referenced from the architecture pages. Each
diagram is single-sourced here and linked from the pages that use it, so it
never needs to be redrawn or duplicated.

## Index

| Diagram | Shows |
|---|---|
| [System Context](system-context.md) | How users, Sisques Account, the identity provider, and apps talk to each other |
| [Login & Token Issuance](login-token-issuance.md) | The login flow from user to issued `access_token` / `refresh_token` |
| [Refresh Rotation](refresh-rotation.md) | Refresh-token rotation and reuse detection |
| [Data Model (ER)](data-model-er.md) | The `app` / `user` / `tenant` / `tenant_membership` / `tenant_invite` schema |
