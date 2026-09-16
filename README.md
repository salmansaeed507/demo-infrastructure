# infrastructure

VPS deploy for the multi-service demo. App repos stay separate; this repo owns Compose and GitHub Actions.

## Stack

`compose/docker-compose.yml` runs on the VPS at `/var/www/compose`:

- **postgres**, **redis** — pulled from Docker Hub
- **api-gateway** — image built in CI, loaded on the VPS (`docker save` / `docker load`, no registry)

Frontend is a static build published to `/var/www/demo` (not Compose).

## Deploy

App repos dispatch here (`repository_dispatch`). You can also run workflows manually in Actions.

| Workflow | What it does |
|---|---|
| **Deploy API Gateway** | Build image → upload to VPS → start postgres/redis → `alembic upgrade head` → start api-gateway |
| **Deploy Frontend** | `npm run build` → rsync `dist/` to `/var/www/demo` |

VPS SSH user needs Docker access (`docker` group or equivalent).

## Secrets (`demo-infrastructure`)

| Secret | Used by |
|---|---|
| `VPS_HOST`, `VPS_USER`, `VPS_SSH_KEY` | Both deploys |
| `LOGIN_TOKEN`, `POSTGRES_PASSWORD` | API gateway `.env` on VPS |
| `VITE_API_URL`, `VITE_API_KEY` | Frontend build |

On `demo-frontend` / `demo-api-gateway`: `INFRA_DISPATCH_PAT` (PAT that can dispatch to this repo).



