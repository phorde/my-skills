# my-skills

Single master repository for autonomous agent skills, custom-tailored for full-stack engineering, clinical AI safety, spec-driven development, and resilient architecture.

Shared across all AI agent harnesses on this machine (Antigravity, OpenCode, Claude Code, Codex, and Gemini CLI). Each harness's `skills/` directory remains a real directory containing per-skill symlinks pointing into this repository — so editing a skill once immediately updates every tool.

---

## Skills Catalog

| Skill | Category | Trigger / Purpose |
| :--- | :--- | :--- |
| **`spec-driven-tdd`** | Architecture & Process | Autonomous SDD + TDD engine. Evidence-based stack selection, parametric modeling (anti-hardcoding), single `PRD.md`, and run-to-completion Red-Green-Refactor loop. |
| **`ui-primitive-composition`** | Frontend Engineering | Enforces design-system-first component assembly. Prioritizes shadcn/ui, Radix primitives, Lucide icons, and semantic Tailwind tokens. Forbids reinventing UI primitives. |
| **`systematic-debugging`** | Diagnostics & Quality | 4-phase scientific root-cause investigation. The Iron Law: *No fixes without root cause investigation first*. Eliminates guesswork and symptom patches. |
| **`clinical-safety-guard`** | Health-Tech & Domain | Enforces clinical assertion stratification (`EXPLICIT_FINDING`, `EXPANDED_NORMAL`, `INFERRED_DIFFERENTIAL`, `FORBIDDEN_FABRICATION`), LGPD/CFM data privacy, and emergency red flags. |
| **`database-migrations`** | Data & Backend | Safe, zero-downtime schema evolution: expand-and-contract, concurrent indexing, batched data backfills, and idempotent DDL across Postgres, SQLite, Prisma, Drizzle, and Go. |
| **`deep-modules`** | Software Architecture | John Ousterhout's *Philosophy of Software Design*: simple interfaces hiding complex implementations. Forbids shallow pass-through abstractions, file splattering, and classitis. |
| **`security-audit`** | Security & Compliance | Threat-modeling and code auditing for vulnerabilities, injection vectors, authorization bypass, tenant exposure, privilege escalation, and unsafe execution. |
| **`verification-planning`** | QA & Verification | Deterministic verification plans, edge case enumeration, and automated test matrices before declaring features complete. |
| **`simplify`** | Code Health | Systematic complexity reduction based on KISS, YAGNI, Chesterton's Fence, and dead-code pruning. |
| **`codemap`** | Codebase Exploration | Generates ultra-compact, token-efficient architectural maps of repository structure and key symbols. |
| **`agent-browser`** | Web Automation & E2E | Fast headless/headed browser automation via `agent-browser` CLI: snapshot, interactive testing, accessibility checks, and UI verification. |
| **`clonedeps`** | Dependency Analysis | Clones and inspects third-party dependencies locally to verify real API behavior without assumptions. |
| **`deepwork`** | Agent Workflow | Structured management for long-running autonomous tasks without context degradation or loops. |
| **`pr-audit`** | Review Gate | Two-axis pull request evaluation: coding standards adherence and specification fidelity. |
| **`worktrees`** | Git Engineering | Git worktree isolation for parallel agent task execution without branch collision. |
| **`loop-engineering`** | Meta-Cognitive | Identifies repetitive agent loops, broken reasoning cycles, and enforces circuit-breaker resets. |
| **`step-back`** | Meta-Cognitive | Pauses execution when unexpected complexity appears, reassessing architecture from first principles. |
| **`deanchor`** | Meta-Cognitive | Detects and neutralizes cognitive anchoring or premature conclusions during technical analysis. |
| **`reflect`** | Retrospective | Systematic post-mortem analysis following feature delivery or defect remediation. |
| **`ai-estimates`** | Operations | Evaluates execution complexity, model routing, and token budget efficiency. |

---

## Layout

Flat structure: one directory per skill containing `SKILL.md` (and any associated scripts, agents, or reference documents):

```
my-skills/
├── agent-browser/
├── ai-estimates/
├── clinical-safety-guard/
├── clonedeps/
├── codemap/
│   └── scripts/
├── database-migrations/
├── deanchor/
├── deep-modules/
├── deepwork/
├── loop-engineering/
├── pr-audit/
│   └── agents/
├── reflect/
├── security-audit/
│   ├── agents/
│   ├── references/
│   └── scripts/
├── simplify/
├── spec-driven-tdd/
├── step-back/
├── systematic-debugging/
├── ui-primitive-composition/
├── verification-planning/
├── worktrees/
├── .gitignore
└── README.md
```

---

## Consumer Harnesses

| Harness | Skills Directory | Link Type |
| :--- | :--- | :--- |
| **Antigravity / Shared** | `~/.agents/skills/` | Per-skill symlink |
| **OpenCode** | `~/.config/opencode/skills/` | Per-skill symlink |
| **Claude Code** | `~/.claude/skills/` | Per-skill symlink |
| **Codex** | `~/.codex/skills/` | Per-skill symlink |
| **Gemini CLI** | `~/.gemini/config/skills/` | Per-skill symlink |
| **Quick Alias** | `~/skills` | Directory symlink → `~/projects/my-skills` |

Per-skill symlinks ensure that harness-specific plugins (such as `oh-my-opencode-slim` in OpenCode or `.system` in Codex) remain completely isolated in their native directories without polluting this repository.

---

## Deliberately NOT in this Repository

- **`oh-my-opencode-slim`**: Native OpenCode runtime plugin living directly in `~/.config/opencode/skills/`.
- **`orca-cli`**: Subsystem harness tool managed directly in `~/.agents/skills/orca-cli/`.
- **Proprietary Credentials & Data**: Credentials, tokens, keys, and private patient/hospital identifiers must never be committed.

---

## Linking Skills to Harnesses

To link all skills into the system harnesses automatically:

```bash
for skill in ~/projects/my-skills/*/; do
  skill_name=$(basename "$skill")
  # Link to shared agents directory
  ln -sfn "$skill" ~/.agents/skills/"$skill_name"
  # Link to OpenCode
  ln -sfn "$skill" ~/.config/opencode/skills/"$skill_name"
  # Link to Claude Code
  mkdir -p ~/.claude/skills
  ln -sfn "$skill" ~/.claude/skills/"$skill_name"
  # Link to Codex
  mkdir -p ~/.codex/skills
  ln -sfn "$skill" ~/.codex/skills/"$skill_name"
  # Link to Gemini
  mkdir -p ~/.gemini/config/skills
  ln -sfn "$skill" ~/.gemini/config/skills/"$skill_name"
done
```

---

## Adding a New Skill

1. Create a directory: `mkdir -p ~/projects/my-skills/<skill-name>`
2. Author `SKILL.md` with standard YAML frontmatter (`name` and `description`).
3. Symlink to active harnesses:
   ```bash
   ln -s ~/projects/my-skills/<skill-name> ~/.agents/skills/<skill-name>
   ln -s ~/projects/my-skills/<skill-name> ~/.config/opencode/skills/<skill-name>
   ```
4. Commit and push to GitHub.
