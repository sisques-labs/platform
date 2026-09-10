# Login & Token Issuance

The login flow from a user opening Sisques Account to an app receiving a
validated `access_token`.

```mermaid
sequenceDiagram
    actor User
    participant App as App (Gardenia/Nexora)
    participant Account as Sisques Account
    participant IdP as Identity provider (Keycloak)

    User->>Account: Open login.sisqueslabs.com
    Account->>IdP: OIDC login (internal)
    IdP-->>Account: Authenticated identity
    Account->>Account: Issue access_token (JWT, 10-15 min) + refresh_token (opaque)
    Account-->>User: Set-Cookie access_token, refresh_token (Domain=.sisqueslabs.com, httpOnly)
    User->>App: Request with access_token cookie
    App->>App: Validate JWT signature via JWKS (GET /.well-known/jwks.json)
    App-->>User: Authenticated response
```

SPA apps that cannot read an `httpOnly` cookie instead call
`GET login.sisqueslabs.com/api/token` (`credentials: include`) to obtain the
JWT in the response body, and send it as `Authorization: Bearer` to their
own API. See [Sessions & Tokens](../architecture/sisques-account/sessions-and-tokens.md)
for the full reasoning.
