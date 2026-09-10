# Diagrams

Standalone Mermaid diagrams referenced from the architecture pages. Each
diagram is single-sourced here and linked from the pages that use it, so it
never needs to be redrawn or duplicated.

## Index

The diagram set is authored incrementally; this table is populated with real
links as each diagram lands.

| Diagram | Shows |
|---|---|
| System Context | How users, Sisques Account, the identity provider, and apps talk to each other |
| Login & Token Issuance | The login flow from user to issued `access_token` / `refresh_token` |
| Refresh Rotation | Refresh-token rotation and reuse detection |
| Data Model (ER) | The `app` / `user` / `tenant` / `tenant_membership` / `tenant_invite` schema |
