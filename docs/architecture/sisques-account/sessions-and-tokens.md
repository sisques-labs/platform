# Sessions & Tokens

- **Domain topology:** every app (Gardenia, Nexora, future apps) lives
  under subdomains of `sisqueslabs.com`. This makes it possible to share a
  cookie with `Domain=.sisqueslabs.com` instead of needing a full OAuth
  redirect flow between apps to propagate a session.
- **Two cookies**, both scoped to `Domain=.sisqueslabs.com`:
  - `access_token` — short-lived JWT (10–15 min), `httpOnly`.
  - `refresh_token` — opaque, long-lived, `httpOnly`, used only by Sisques
    Account.
- **Refresh token rotation and revocation:** reuses the pattern already
  proven in production in `gardenia-api` — rotation on every use with a
  pessimistic lock, plus reuse detection (using an already-consumed
  `refresh_token` invalidates the entire session chain, a signal of token
  theft). No new mechanism is designed for the platform without a concrete
  need that justifies it; this one already ships with real production
  bugfixes resolved (including a race condition).
- The platform supports **two consumption patterns** for apps, without
  duplicating logic:
  - **App with its own backend (SSR):** reads `access_token` directly on
    every request and validates the signature locally with Sisques
    Account's public key, published via a standard JWKS endpoint
    (`GET /.well-known/jwks.json`). Each app caches the key(s); this
    allows rotating the signing key without touching or redeploying any
    app, in line with the goal of switching identity providers without
    touching the apps. A fixed public key via env var was rejected because
    it would require manually reconfiguring and redeploying every app on
    each rotation.
  - **SPA app + its own API:** JS cannot read an `httpOnly` cookie, so it
    calls `GET login.sisqueslabs.com/api/token` (`credentials: include`)
    to get the JWT in the response body and sends it as
    `Authorization: Bearer` to its own API, which validates it the same
    way as the SSR case.
  - Silent refresh in both cases: `POST login.sisqueslabs.com/refresh`
    (`credentials: include`) uses the `refresh_token` to issue a new
    `access_token` without user interaction.
- **`httpOnly`** was chosen for `access_token` (shielded against XSS) over
  the alternative of leaving it readable by JS, accepting the extra step
  that implies for the SPA case.
- Reasoning for short tokens + refresh instead of a "live call" to the
  platform on every request, or long-lived tokens: a balance between
  resilience (apps do not depend on the platform responding on every
  click) and reasonable consistency (access revocation is reflected within
  minutes, not instantly).

The [Login & Token Issuance](../../diagrams/login-token-issuance.md) and
[Refresh Rotation](../../diagrams/refresh-rotation.md) diagrams show these
flows step by step. The underlying decisions are recorded in ADR-0002
(Account issues its own JWT), ADR-0005 (JWKS endpoint for key
distribution), ADR-0006 (refresh rotation with reuse detection), and
ADR-0008 (cookies scoped to `.sisqueslabs.com`).
