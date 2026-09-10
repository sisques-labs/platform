# System Context

How users, Sisques Account, the identity provider, and apps relate to each
other. Apps never talk to the identity provider directly — only to Sisques
Account, which issues its own signed tokens.

```mermaid
flowchart TD
    User([User]) -->|OIDC login| Account["Sisques Account<br/>login.sisqueslabs.com"]
    Account -->|"OIDC (internal)"| IdP["Identity provider adapter<br/>Keycloak today / Cognito or other later"]
    Account -->|"issues its own JWT<br/>access_token + refresh_token"| Apps["Gardenia / Nexora / other apps"]
```

See [Sisques Account overview](../architecture/sisques-account/index.md) for
the reasoning behind this shape, and ADR-0001 (Sisques Account owns
identity) and ADR-0003 (IdP behind a swappable adapter) for the locked
decisions.
