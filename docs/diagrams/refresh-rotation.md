# Refresh Rotation

Refresh-token rotation with reuse detection, reusing the pattern already
proven in production in `gardenia-api`.

```mermaid
sequenceDiagram
    participant App
    participant Account as Sisques Account
    participant DB as Account DB

    App->>Account: POST login.sisqueslabs.com/refresh (credentials: include, refresh_token cookie)
    Account->>DB: Lock refresh_token row (pessimistic lock)
    alt refresh_token valid and unused
        Account->>DB: Mark refresh_token consumed, issue new refresh_token
        Account-->>App: New access_token + new refresh_token
    else refresh_token already consumed (reuse detected)
        Account->>DB: Invalidate entire session chain
        Account-->>App: 401 Unauthorized, session revoked
    end
```

Reusing an already-consumed `refresh_token` invalidates the whole session
chain — a signal of token theft. See
[Sessions & Tokens](../architecture/sisques-account/sessions-and-tokens.md)
and [ADR-0006](../adr/0006-refresh-rotation-reuse-detection.md) (refresh
rotation with reuse detection).
