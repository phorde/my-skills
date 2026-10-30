# User Preferences & Agent Behavioral Guidelines

Canonical memory of user preferences, coding standards, and architectural invariants for Pedro Henrique (Phorde). All agents operating in this workspace must load and obey these rules.

---

## 1. Identity, Communication & Language Convention

* **User:** Pedro Henrique (Phorde)
* **Timezone:** Brazil — Brasília Time (`America/Sao_Paulo`, UTC-3). Always use Brasília time for logs, commits, cron jobs, and timestamps.
* **Conversational Language:** Brazilian Portuguese (pt-BR) when communicating with the user.
* **Internal Agent Artifacts Language:** Technical English for all internal docs (`HANDOFF.md`, `CHANGELOG.md`, `PREFERENCES.md`, code comments, architecture docs) to maximize BPE tokenizer efficiency and LLM comprehension.
* **Response Style:**
  * Concise, pragmatic, direct to the point. No fluff, no boilerplate disclaimers.
  * Clean GitHub Flavored Markdown formatting.
  * Mandatory clickable links using `file:///absolute/path` for all referenced files and code symbols.
  * Action-first: verify before assuming; ask only on critical architectural ambiguities.

---

## 2. Engineering & Architecture Standards

* **Philosophy:** Local-first, declarative, minimal, high-performance, deep modules with simple interfaces (*Philosophy of Software Design*). Anti-bloat (strictly no heavy PaaS like Coolify/Dokploy).
* **Backend & Scripting:** Node.js / TypeScript, Python managed via `uv` (never slow virtualenvs with pip), POSIX-compliant Bash.
* **Frontend:** Next.js (App Router), Tailwind CSS, TypeScript, Radix UI / shadcn/ui.
* **Containers & Hosting:** Declarative Docker Compose, Caddy Reverse Proxy with automated HTTPS (Let's Encrypt).
* **Database & Persistence:** SQLite / libSQL / PostgreSQL, atomic safe migrations.

---

## 3. VPS & Infrastructure Invariants (Oracle Cloud ARM64)

* **Host:** `marvin` (`129.146.68.56` / `*.phorde.com.br` / Tailscale `100.67.85.3`).
* **Hardware:** 4 OCPUs ARM64 (`aarch64`), 24 GB RAM, 200 GB NVMe SSD.
* **Network Isolation:** All Docker container ports MUST bind strictly to localhost (`127.0.0.1:<port>`). NEVER expose `0.0.0.0`.
* **Routing:** Public traffic goes exclusively through Caddy and Cloudflare Wildcard DNS (`*.phorde.com.br`).
* **CI/CD:** Official projects use native ARM64 Git push-to-deploy via the canonical `deploy` CLI.
* **Firewall Integrity:** Rule ordering in `/etc/iptables/rules.v4` must always preserve `ACCEPT` rules prior to final `REJECT`.
* **Four Pillars Filesystem:**
  * `~/apps/`: Production Docker Compose stacks.
  * `~/projects/`: Flat source code repositories.
  * `~/scripts/`: Operational cron & maintenance routines.
  * `~/backups/`: Rotated zstd snapshots.

---

## 4. Active Project Domains

1. **Hub Phorde (`~/projects/hub`):** Central platform and unified personal/professional services dashboard.
2. **Residência Médica 2027 (`~/projects/residencia2027`):** High-stakes medical residency exam prep platform in Brazil. Requires strict medical domain accuracy and fast UX.
3. **VPS Handbook (`~/projects/vps-handbook`):** Infrastructure as code, audit logs, runbooks, and versioned `CHANGELOG.md`.

---

## 5. Session Lifecycle & Triad Memory Protocol

* **Onboarding (New Session Start):** Read `~/.agents/HANDOFF.md` and `~/.agents/PREFERENCES.md` to instantly restore working context with minimal token overhead.
* **Offboarding (Session End or Quota Limit):** Triggered when user says closing phrase (e.g. *"Obrigado, por hoje é só!"*) or when API quota runs out:
  1. Update `CHANGELOG.md` in `~/projects/vps-handbook/`.
  2. Write updated task state to `~/.agents/HANDOFF.md`.
  3. Update `~/.agents/PREFERENCES.md` if new user preferences were learned.
  4. Commit changes to Git in `vps-handbook`.
  *Note:* Can be executed locally via the `handoff` CLI using the local Ollama model to avoid spending cloud tokens or when quota limit occurs.
