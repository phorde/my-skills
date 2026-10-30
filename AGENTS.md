# Diretrizes Gerais e Infraestrutura — Workspace phorde

Este documento é a **fonte única da verdade global** para todos os agentes de IA (Antigravity, Claude Code, OpenAI Codex, OpenCode, Cursor, Copilot, etc.) operando neste workspace e na infraestrutura remota.

> **Escopo Hierárquico (Padrão SOTA `agents.md`):**
> Este arquivo define as diretrizes globais do SO e infraestrutura da VPS. Repositórios individuais sob `~/projects/<nome>/` contêm seu próprio `AGENTS.md` com comandos de build, testes e invariantes locais de cada aplicação (*closest-file-wins*).

---

## 1. Ambiente de Execução & Invariantes Críticas

* **Host:** VPS Oracle Cloud Ampere A1 (`marvin` / `129.146.68.56` / IP Tailscale `100.67.85.3`).
* **Hardware:** 4 OCPUs ARM64 (`aarch64`), 24 GB RAM, 200 GB SSD NVMe, Ubuntu 24.04 LTS.
* **Execução Local:** Você está executando **diretamente dentro da VPS**. Não execute comandos SSH em loopback; utilize o shell bash local.

### Invariantes Invioláveis (O que os Agentes NUNCA devem fazer):
1. **Rede Localhost Obrigatória:** Portas de containers Docker devem **obrigatoriamente ser vinculadas apenas ao localhost** (`127.0.0.1:<porta-livre>:<porta-container>`). Nunca exponha portas para `0.0.0.0`. O Caddy gerencia o acesso público, TLS (Let's Encrypt), HTTP/2 e HTTP/3.
2. **VPS é para Executar, NÃO para Compilar:** Builds de produção pesados rodam em runners nativos ARM64 no GitHub Actions (`ubuntu-24.04-arm`). Nunca execute `docker build` de imagens de produção localmente na VPS.
3. **Zero Painéis PaaS:** NÃO instale Dokploy, Coolify, Portainer ou CapRover. A infraestrutura é mantida declarativa, leve e rápida via Docker Compose + CLI `deploy`.
4. **Sem Redes Redundantes:** NÃO instale Cloudflare Tunnels (`cloudflared`) redundantes. O tráfego direto via DNS Wildcard (`*.phorde.com.br`) e Caddy é a arquitetura padrão oficial. Nunca altere o Cloudflare para criar subdomínios.
5. **Privilégios Docker:** NÃO execute `sudo docker`. O usuário `ubuntu` já pertence ao grupo `docker`.
6. **Segurança de SSH e Firewall:** NÃO altere a porta do SSH (22). As regras do `/etc/iptables/rules.v4` devem sempre manter as regras `ACCEPT` antes da regra final `REJECT`.
7. **Isolamento da Raiz:** NUNCA crie pastas avulsas em `/home/ubuntu/`. Todo código reside em `~/projects/` e serviços em produção residem em `~/apps/`.

---

## 2. Pipeline Canônico de Deploy & Produção (`deploy`)

O deploy de qualquer aplicação com repositório no GitHub é 100% automatizado, seguro e agent-friendly: **qualquer `git push origin main` compila no GitHub Actions ARM64, publica no GHCR e executa deploy com health-gate atômico na VPS**.

### Comandos Oficiais da CLI `deploy`:
```bash
# Inicializar CI/CD e Caddy em um novo projeto (1 comando):
deploy init <caminho-do-projeto> <subdominio> <porta-local>

# Executar deploy determinístico de uma imagem OCI:
deploy run <app> <ghcr.io/phorde/app:tag>

# Inspecionar saúde, versão ativa e histórico de releases:
deploy status <app>

# Rollback atômico imediato para a versão anterior:
deploy rollback <app>

# Listar todas as aplicações gerenciadas:
deploy list
```

### Como Operar no Dia a Dia:
* **Desenvolvedores e Agentes:** Apenas façam commit e `git push origin main`.
* **Health-Gate em 3 Camadas:** O deploy só é considerado concluído se passar por:
  1. Docker Container Healthcheck (`healthy`).
  2. Loopback HTTP local (`http://127.0.0.1:<porta>/api/health` -> HTTP 200).
  3. Public HTTPS TLS via Caddy (`https://<subdominio>.phorde.com.br/api/health` -> HTTP 200).
  *Se qualquer camada falhar, a CLI `deploy` reverte automaticamente para a versão anterior sem intervenção manual.*

---

## 3. Taxonomia Canônica dos 4 Pilares

Siga rigorosamente a estrutura plana abaixo (detalhes de busca rápida em [`WORKSPACE_DISCOVERY.md`](file:///home/ubuntu/WORKSPACE_DISCOVERY.md)):

```text
/home/ubuntu/
├── apps/                  # 1. Aplicações em produção rodando Docker Compose (porta 127.0.0.1:<porta>)
│   ├── desktop/           #    KasmVNC Webtop Desktop (porta 3010)
│   ├── hub/               #    Produção do Hub (porta 3000)
│   ├── ollama/            #    LLM local (porta 11434, keep-alive 5m)
│   └── study-ai/          #    Produção do Study AI (porta 8000)
├── projects/              # 2. Códigos-fonte e desenvolvimento (estrutura plana, sem burocracia)
│   ├── hub/               #    Repositório do Hub
│   ├── residencia2027/    #    Plataforma Residência Médica 2027
│   ├── skills/            #    Biblioteca oficial de skills dos agentes (phorde/my-skills)
│   ├── vps-handbook/      #    Documentação e infraestrutura como código
│   └── _archive/          #    Projetos legados e templates clonáveis (ignorar em buscas)
├── scripts/               # 3. Scripts operacionais e automações do cron
│   ├── backup.sh          #    Snapshot semanal zstd com rotação dos últimos 3
│   ├── cleanup.sh         #    Limpeza semanal de Docker (teto 5GB), journals e caches
│   ├── optimize.sh        #    Diagnóstico diário de RAM, disco, serviços e zumbis
│   └── update.sh          #    Patches autônomos de segurança do Ubuntu
├── backups/               # 4. Snapshots semanais comprimidos em .tar.zst (e _legacy/)
├── skills -> /home/ubuntu/projects/skills # Symlink de conveniência
├── .agents/skills -> /home/ubuntu/projects/skills # Symlink de descoberta para agentes
├── .ignore                # Filtro global para ripgrep/fd/fzf ignorando ruídos (node_modules, venvs, caches)
└── WORKSPACE_DISCOVERY.md # Guia de busca econômica para economia de tokens
```

---

## 4. Ciclo de Vida da Sessão, Idiomas e Memória Tripartite

Para máxima economia de tokens, compreensão cruzada entre diferentes modelos (Claude, GPT, Gemini, Qwen, DeepSeek) e persistência total contra esgotamento de cotas (*quota limits*):

### Convenção de Idiomas (BPE Tokenizer Optimization):
* **Comunicação com o Usuário:** Português do Brasil (pt-BR).
* **Documentos Técnicos Internos dos Agentes:** **Inglês técnico conciso**. Escrever `HANDOFF.md`, `CHANGELOG.md`, `PREFERENCES.md` e comentários de código sempre em inglês. O BPE tokenizer do inglês consome ~40% menos tokens que idiomas latinos e reduz ambiguidades conceituais entre agentes.

### Onboarding (Início de Qualquer Sessão):
Ao iniciar uma nova interação, o agente DEVE ler:
1. [`/home/ubuntu/.agents/HANDOFF.md`](file:///home/ubuntu/.agents/HANDOFF.md): Para retomar imediatamente a tarefa ativa, verificar onde paramos e executar os próximos passos sem gastar tokens de brainstorming redundante.
2. [`/home/ubuntu/.agents/PREFERENCES.md`](file:///home/ubuntu/.agents/PREFERENCES.md): Para carregar o perfil, stack e regras do usuário.

### Offboarding e Quota Limit (Fechamento de Sessão):
Sempre que o usuário:
* Digitar `/quit` ou `/exit`
* Sinalizar encerramento (ex: *"Obrigado, por hoje é só!"*, *"Finalizamos por hoje"*, *"Valeu, encerramos"*)
* OU se ocorrer esgotamento de cota da API em nuvem (*quota limit* / HTTP 429):

**O agente atuante DEVE OBRIGATORIAMENTE disparar a CLI `handoff` antes de encerrar:**
```bash
handoff
# Ou com notas específicas: handoff "rotas caddy verificadas, pendente teste ssl"
```

### O que o comando `handoff` faz localmente (CPU ARM via Ollama, custo zero de tokens):
1. Sincroniza o repositório com o GitHub (`git pull --rebase origin main`).
2. Atualiza a timeline auditável em [`~/projects/vps-handbook/CHANGELOG.md`](file:///home/ubuntu/projects/vps-handbook/CHANGELOG.md) (com symlink em `~/CHANGELOG.md`).
3. Sobrescreve [`~/.agents/HANDOFF.md`](file:///home/ubuntu/.agents/HANDOFF.md) com o estado quente e próximos passos para o próximo agente.
4. Adiciona novas preferências aprendidas em [`~/.agents/PREFERENCES.md`](file:///home/ubuntu/.agents/PREFERENCES.md).
5. Commita atomicamente e **faz push imediato para o GitHub** (`git push origin main` em `phorde/vps-handbook`).
6. **Confirmar ao Usuário:** Notificar sucintamente em português que a sessão foi consolidada, versionada pelo modelo local e sincronizada no GitHub.

---

## 5. Convenções de Interoperabilidade Multi-Ferramenta

Para garantir que todos os agentes (independente de qual CLI ou IDE for acionada) leiam exatamente as mesmas regras:
* `~/CLAUDE.md` e `~/.cursorrules` são symlinks apontando para `/home/ubuntu/AGENTS.md`.
* `~/.agents/AGENTS.md` é symlink para `/home/ubuntu/AGENTS.md` (descoberta do padrão aberto `agents.md`).
* Cada projeto em `~/projects/<nome>/` possui symlinks idênticos espelhando seu respectivo `AGENTS.md` local.
