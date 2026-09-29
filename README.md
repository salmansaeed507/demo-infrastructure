# infrastructure

VPS deploy for the multi-service demo. App repos stay separate; this repo owns Compose and GitHub Actions.

## Stack

`compose/docker-compose.yml` runs on the VPS at `/var/www/compose`:

- **postgres**, **redis**, **rustfs** — pulled from Docker Hub
- **api-gateway**, **customer-support-agent** — images built in CI, loaded on the VPS (`docker save` / `docker load`, no registry)

Frontend is a static build published to `/var/www/demo` (not Compose).

## Deploy

App repos dispatch here (`repository_dispatch`). You can also run workflows manually in Actions.

| Workflow | What it does |
|---|---|
| **Deploy API Gateway** | Build image → upload to VPS → start postgres/redis/rustfs → `alembic upgrade head` → start api-gateway |
| **Deploy Customer Support Agent** | Build image → upload to VPS → start postgres → `alembic upgrade head` → start customer-support-agent |
| **Deploy Frontend** | `npm run build` → rsync `dist/` to `/var/www/demo` |

VPS SSH user needs Docker access (`docker` group or equivalent).

## Secrets (`demo-infrastructure`)

| Secret | Used by |
|---|---|
| `VPS_HOST`, `VPS_USER`, `VPS_SSH_KEY` | All deploys |
| `LOGIN_TOKEN`, `POSTGRES_PASSWORD` | API gateway / customer-support-agent `.env` on VPS |
| `RUSTFS_ACCESS_KEY`, `RUSTFS_SECRET_KEY` | rustfs `.env` on VPS (via API gateway deploy) |
| `VITE_API_URL`, `VITE_API_KEY` | Frontend build |

On `demo-frontend` / `demo-api-gateway` / `demo-customer-support-agent`: `INFRA_DISPATCH_PAT` (PAT that can dispatch to this repo).



