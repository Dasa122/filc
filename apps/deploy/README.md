# Deploy (Coolify)

Coolify deployment definitions that are not part of a workspace package.

## `compose.yml` — Chronos + per-preview database

Deploy this as a **Docker Compose** application in Coolify (Git source,
`filcdev/filc`, compose file `apps/deploy/compose.yml`). Coolify duplicates a
Docker Compose application per pull-request preview, so every preview gets its
own `postgres` container and `postgres-data` volume — a separate database per
deployment, created and removed with the preview.

- Coolify generates `SERVICE_USER_POSTGRES` / `SERVICE_PASSWORD_64_POSTGRES`
  per resource and injects them into both services. Do not set them.
- Chronos runs its Drizzle migrations on boot, so a new preview database is
  migrated automatically.
- Set `CHRONOS_BASE_URL` to the deployment origin (no `/api` path) — better-auth
  derives its routing base path from that URL.
- The optional `CHRONOS_*` variables Coolify creates from `${VAR:?}` are
  required and must be filled in before the stack will deploy.

### Notes

- Each preview runs its own Postgres (RAM/disk per PR); the server must fit
  production plus previews.
- Deleting a preview removes the stack and its volumes (preview data is
  ephemeral).
- The build context is `../..` (the repository root) because the Chronos
  Dockerfile prunes the monorepo.
