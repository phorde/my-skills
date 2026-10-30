---
name: vps-deploy
description: Canonical deployment and production lifecycle engine on VPS marvin (ARM64). Use whenever deploying, updating, onboarding, checking health, or rolling back any web application or microservice on the VPS. Enforces GitOps push-to-deploy, native ARM64 compilation, GHCR immutable artifacts, Caddy reverse-proxy routing, and the official `deploy` CLI.
---

# VPS Deployment & Production Lifecycle Engine

Universal runbook and operating procedure for application deployment on VPS `marvin` (`phorde.com.br`).

---

## 1. Architectural Invariants (Non-Negotiable)

1. **VPS is for Running, NOT Compiling:** Heavy compilation, Docker builds, and testing run on GitHub Actions native ARM64 runners (`ubuntu-24.04-arm`). Never run raw `docker build` of production images on the VPS.
2. **Localhost Binding Only:** All application containers must bind strictly to `127.0.0.1:<port>:<container-port>`. Never expose `0.0.0.0` or raw ports publicly. Caddy handles TLS termination, HTTP/2, HTTP/3, and reverse-proxying.
3. **No Heavy PaaS Panels:** Do not install Dokploy, Coolify, Portainer, or CapRover. The system is declarative, lightweight, and managed via Docker Compose + `deploy` CLI.
4. **Any-Device Git Push to Deploy:** Deploys are cloud-driven. A `git push origin main` from ANY device (VPS, laptop, desktop, GitHub web) automatically triggers the deployment.
5. **3-Layer Health-Gate & Atomic Rollback:** Every deploy must verify Container Health -> Loopback HTTP (`127.0.0.1:<port>`) -> Public HTTPS TLS. If any layer fails within 60s, the engine automatically rolls back to the previous stable release.

---

## 2. The Official `deploy` CLI Reference

The VPS provides the official `/usr/local/bin/deploy` engine:

| Command | Purpose |
| :--- | :--- |
| `deploy init <caminho> <subdominio> <porta>` | One-shot onboarding of a project (creates Dockerfile, docker-compose, Caddy route, and CI/CD workflow). |
| `deploy run <app> <imagem-oci>` | Deterministic release execution with 3-layer health-gate and automated rollback. |
| `deploy status <app>` | Inspects live release state (`.deploy-state.json`), container health, and last logs. |
| `deploy rollback <app>` | Instantly rolls back to the previously recorded stable image. |
| `deploy list` | Overview table of all deployed applications and container statuses. |

---

## 3. Onboarding a New Application (1 Command)

To onboard any new service in `~/projects/<nome>`:
```bash
deploy init ~/projects/<nome> <subdominio> <porta-local-livre>
```
*Example:* `deploy init ~/projects/hub personal 3000`

### What `deploy init` Automates:
1. Scans project type and writes a production-grade multi-stage `Dockerfile`.
2. Sets up `~/apps/<nome>/docker-compose.yml` bound strictly to `127.0.0.1:<porta-local>`.
3. Creates initial `~/apps/<nome>/.env` template.
4. Registers the Caddy route (`<subdominio>.phorde.com.br -> 127.0.0.1:<porta-local>`) via `register-route`.
5. Writes `.github/workflows/deploy.yml` with native ARM64 build, GHCR publishing, and Tailscale deploy trigger.

---

## 4. Daily Development & Delivery Workflow

### From Any Device (Desktop, Laptop, VPS, Agent):
```bash
git checkout main
# Faça as alterações no código...
git add .
git commit -m "feat: sua nova feature"
git push origin main
```

### What Happens Automatically in GitHub Actions:
1. **Runner `ubuntu-24.04-arm`:** Builds OCI container natively on 64-bit ARM with layer caching (`type=gha`).
2. **GHCR:** Pushes immutable tag `ghcr.io/phorde/<app>:sha-<commit>` and updates `latest`.
3. **Tailscale Connection:** Runner joins the private Tailnet as an ephemeral device using `TAILSCALE_AUTHKEY`.
4. **SSH Trigger:** Runner runs `deploy run <app> <imagem>` on `100.67.85.3` passing a short-lived scoped GHCR token.
5. **Verification & Audit:** VPS runs 3-layer health checks, marks `status: HEALTHY` in `.deploy-state.json`, and completes the run with exit code 0.

---

## 5. Diagnostics & Emergency Troubleshooting

### Checking Status:
```bash
deploy status <app>
```

### Manual Rollback:
If an issue arises after deployment, rollback takes less than 3 seconds:
```bash
deploy rollback <app>
```

### Checking Application Logs:
```bash
cd ~/apps/<app> && docker compose logs -f --tail=100
```
