# Sisques Labs Platform

Sisques Labs Platform is the shared identity and tenancy layer behind the
Sisques Labs ecosystem: a single-owner set of personal apps (Gardenia,
Nexora, and future apps) that share one login and one notion of "tenant"
through **Sisques Account**. This repository is the canonical, versioned
home for platform-wide architecture decisions.

Full architecture, decision records (ADRs) and diagrams are published as a
site: **[sisques-labs.github.io/platform](https://sisques-labs.github.io/platform/)**
(source: [`docs/`](docs/index.md)).

## Repository map

| Repo | Role |
|---|---|
| **Gardenia** | Personal plant-care app; the first app in the ecosystem. Its `gardenia-api` currently has its own working `contexts/auth` + tenant-repository implementation (JWT + refresh + OAuth), not yet migrated to Sisques Account. |
| **Nexora** | Another personal app in the Sisques Labs ecosystem, intended to share identity and tenancy through Sisques Account. |
| **Sisques Account** | The shared identity and tenancy service for the whole platform. Repo `sisqueslabs/account`; domain `login.sisqueslabs.com`. |
| **account-api** | Sisques Account's backend (NestJS): identity-provider adapter, tenancy logic, JWT issuance/validation, admin endpoints. |
| **account-web** | Sisques Account's frontend (Next.js): public login/registration pages plus an `/admin` section for platform admins, on the same session. |
| **Portero** | Another app in the Sisques Labs ecosystem. |

See [`docs/adr/`](docs/adr/index.md) for the decisions behind the Sisques
Account design; the full architecture is published on the
[docs site](https://sisques-labs.github.io/platform/) above.
