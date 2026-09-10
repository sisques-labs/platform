# Platform Overview

## Naming

- **Sisques Labs Platform** — the overall ecosystem concept/vision (identity
  + apps + shared infrastructure). A "marketing" name, not a service.
- **Sisques Account** — the concrete service described in this section.
  Repo: `sisqueslabs/account`. Domain: `login.sisqueslabs.com` (or
  `accounts.sisqueslabs.com`).

## Scope and principles

- Ecosystem of personal projects, single owner. It is **not** opened to
  external developers or third-party apps — both ends (platform and every
  app) are always controlled by the same owner.
- Each app decides whether it integrates with the platform or not (e.g.
  DaysOff could keep running without login).
- General philosophy: build it ourselves, even if it takes longer — except
  the password/MFA management piece, which is delegated (see
  [Identity provider](sisques-account/index.md#identity-provider)). This is
  both a product and a learning project.
