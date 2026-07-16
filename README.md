# workflows

Reusable GitHub Actions used by the personal `*.w-ilyas.site` projects. Call them from another repo with `uses: Wadjinny/workflows/.github/workflows/<name>.yml@main`.

## Workflows

| Workflow | Purpose |
| -------- | ------- |
| `deploy-pnpm-vite.yml` | Install with pnpm, build a Vite app, rsync `dist/` over SSH |
| `deploy-static-html.yml` | Rsync a static HTML directory over SSH |
| `deploy-caddy-site.yml` | Upload a site Caddyfile and reload Caddy |
| `docker-build-push.yml` | Build a Docker image and push to GHCR |
| `docker-deploy-ssh.yml` | Copy `compose.yml` + env over SSH, then `docker compose pull/up` |

## Common secrets

Most deploy workflows expect:

- `SSH_HOST`, `SSH_USER`, `SSH_KEY` (and optional `SSH_PORT`)
- `SSH_DEPLOY_DIR` — parent directory on the server; files land in `$SSH_DEPLOY_DIR/<repo-name>`
- Optional `DOTENV` — written as `.env` before build or compose deploy

Docker deploy additionally accepts `GHCR_TOKEN` for private pulls.

## Example (Vite frontend + Caddy)

```yaml
jobs:
  frontend:
    uses: Wadjinny/workflows/.github/workflows/deploy-pnpm-vite.yml@main
    with:
      VITE_PROJECT_DIR: frontend
    secrets: inherit

  caddy:
    needs: [frontend]
    uses: Wadjinny/workflows/.github/workflows/deploy-caddy-site.yml@main
    with:
      caddy_file: Caddyfile
      remote_file: /etc/caddy/sites/my-app.conf
    secrets: inherit
```
