# infrastructure

Production/VPS orchestration for the multi-service demo.

Repos are public — no token is needed to clone `demo-frontend`.

## Frontend deploy (central dispatch)

`frontend` only sends a `repository_dispatch` event. Build and VPS publish run here.

### Secrets on this repo (`demo-infrastructure`)

| Secret | Purpose |
|---|---|
| `VITE_API_URL` | Frontend build-time API base URL |
| `VITE_API_KEY` | Frontend build-time API key |
| `VPS_HOST` | VPS hostname/IP |
| `VPS_USER` | SSH user |
| `VPS_SSH_KEY` | Private SSH key for the VPS |

### Secret on `demo-frontend`

| Secret | Purpose |
|---|---|
| `INFRA_DISPATCH_PAT` | PAT that can create `repository_dispatch` on this repo. For public repos, classic PAT with `public_repo` is enough (or a fine-grained PAT with access to `demo-infrastructure` and permission to trigger workflows). |

`GITHUB_TOKEN` from the frontend workflow cannot dispatch to another repo — a PAT is still required even when both repos are public.

Remove old deploy secrets (`VITE_*`, `VPS_*`) from `demo-frontend` once this is live — they belong only here.

### Manual run

Actions → **Deploy Frontend** → Run workflow (optional `ref`, default `main`).
