# Changelog – ams.identityaccess

All notable changes to this product are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

ams.identityaccess is the standalone identity provider for the ams products
(IdentityServer, AdminPortal, ClientHub, EmailSender).

## [3.4.1] – 2026-10-23

### Fixed
- **The NServiceBus license is renewed.** The license shipped so far was a perpetual
  license whose upgrade protection had already expired in 2022. All services now contain
  the renewed subscription license, valid until 2027-10-01.
  **Effect:** The services start with a valid license; no action is required.

## [3.4.0] – 2026-10-23

### Added
- **The ProjectImport API is registered in the identity provider.** The web BFF forwards
  `/api/projectimport/*` to the ProjectImport API and uses token exchange (RFC 8693) to
  obtain a token with the audience `api://projectimport`. Without a matching API resource
  and scope this exchange failed and the BFF answered every request with an empty 502.
  New are the API resource `api://projectimport`, the scope `api.projectimport` and its
  release to the web BFF client. The migrator adds the entries on every run.
  **Effect:** Installations and updates at this version no longer need a manual SQL
  script to make project import reachable through the BFF.

## [3.3.3] – 2026-09-03

### Fixed
- **Installation no longer aborts when the database login owns the database.** Before
  creating the runtime user, the migrator only checked whether a user *of that name*
  already existed. When the login owns the database it is already registered there as
  `dbo`, so nothing was found under its own name and SQL Server rejected the creation with
  `The login already has an account with the user name 'dbo'` (error 15063) — the
  installation failed reproducibly.
  The existing mapping is now detected by the login's identifier (SID) instead of by name:
  if the login is registered as `dbo`, user creation, role and permissions are skipped —
  as the owner it already has full access. If it is registered under a different user
  name, that user is used.
  **Effect:** installations and updates against databases owned by the application login
  now complete.

## [3.3.2] – 2026-07-17

### Fixed
- **The session-lifetime fix now also applies to the normal login flow.** The
  `refresh_expires_in` emission added in 3.3.1 was gated on `offline_access` appearing
  in the requested scopes. IdentityServer4 does not surface that scope there, so the
  **authorization_code login flow** never received the hint and the BFF session still
  expired after ~1 hour. `refresh_expires_in` is now emitted for the
  **authorization_code** and **refresh_token** grants, guarded by the client's
  `AllowOfflineAccess` setting.
  **Effect:** signed-in users stay logged in for as long as their refresh token is valid
  (days instead of ~1 hour) — including via the regular sign-in flow.

## [3.3.1] – 2026-07-16

### Fixed
- **The BFF session now outlives the access-token lifetime.** Unlike Keycloak,
  IdentityServer4 does not report the refresh-token lifetime in its token response.
  The Web BFF uses that value to size its server-side token-vault TTL; without it the TTL
  collapsed to the short access-token lifetime, so the session died ~1 hour after login
  even though the refresh token was valid for days. A new `ICustomTokenRequestValidator`
  appends a Keycloak-compatible `refresh_expires_in` (derived from the client's configured
  refresh-token lifetime) whenever a refresh token is issued. Token-exchange responses are
  left unchanged.

## [3.3.0] – 2026-07-02

### Changed
- **IdentityAccess now runs self-contained in a single database by default.** The separate
  NServiceBus transport database has become **optional**. New variable
  **`DB_TRANSPORT_NAME` (optional)**:
  - **leave blank** → IdentityAccess uses its persistence database (`DB_NAME`) for queues
    and subscriptions as well (recommended for standalone operation);
  - **set** → only the shared `dbo.SubscriptionRouting` table lives in the transport
    catalog — for cross-context pub/sub (e.g. together with the ams.project stack).
- **Unified subscription-routing placement.** Migrator and runtime address
  `dbo.SubscriptionRouting` identically; the table is created **idempotently** in the
  correct catalog — by IdentityAccess or the platform stack, whichever runs first. No more
  orphaned duplicate tables.
- **`SERVICE_BUS_CATALOG` is gone.** The subscription catalog is now derived from
  `DB_TRANSPORT_NAME` (a single source of truth).

### Fixed
- **Cross-context pub/sub in the rc channel restored.** The host services read the catalog
  from `SERVICE_BUS_CATALOG`, but the rc deployment provided `DB_TRANSPORT_NAME` — leaving
  the catalog empty at runtime, so `SubscriptionRouting` fell back to each service's own
  database and pub/sub silently broke.
- **Missing grant added:** the runtime user now reliably receives `dbo.error` on the
  persistence database (previously a latent gap).
- Removed dead/duplicate transport queue tables and the orphaned default
  `IdentityAccess.SubscriptionRouting` table.

### Operator note
- Existing standalone deployments are unaffected (self-contained). If you previously set
  `SERVICE_BUS_CATALOG`, switch to `DB_TRANSPORT_NAME`.

## [3.2.0] – 2026-05-11

- Initial release of IdentityAccess as a standalone RSGO product
  (IdentityServer, AdminPortal, ClientHub, EmailSender). Earlier history is not
  captured in this changelog.
