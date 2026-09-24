# Wednesday Agent Skills

**Transform any repository into an AI-aware, intelligent environment** — a codebase that any AI agent can jump into on day one without making junior-level mistakes.

```bash
npx @wednesday-solutions-eng/ai-agent-skills install
```

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Node](https://img.shields.io/badge/node-%3E%3D18-brightgreen.svg)](https://nodejs.org/)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)

---

## Table of Contents

- [What Is This](#what-is-this)
- [What You Get After Install](#what-you-get-after-install)
- [Why Use This](#why-use-this)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [How to Use It](#how-to-use-it)
- [Skills Reference](#skills-reference)
- [Common Scenarios](#common-scenarios)
- [CLI Reference](#cli-reference)
- [Configuration & Environment Variables](#configuration--environment-variables)
- [Project Layout After Install](#project-layout-after-install)
- [Git Hooks](#git-hooks)
- [Repository Structure](#repository-structure)
- [Development](#development)
- [Testing](#testing)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [License](#license)

---

## What Is This

Most AI agents in large codebases fail in the same ways: they hallucinate structure, waste tokens re-reading files they have already seen, and make changes without knowing what will break. This system solves all three.

It works by pre-computing a **structural dependency graph** (SQLite, AST-based, zero LLM) of your entire codebase once, then giving every AI agent — Claude Code, Cursor, Gemini CLI, GitHub Copilot — a precise manual for how to work in *your specific project*. From that point on, structural questions are answered from the graph in milliseconds, instead of re-reading source files.

Key properties:

- **No API key needed** for the skills themselves — skills are plain Markdown instructions executed by your IDE's intelligence engine.
- **Deterministic graph** — the dependency graph is built with AST parsing (not embeddings), so results are exact and reproducible.
- **Auto-maintained** — `post-commit` / `post-merge` hooks keep the graph in sync in under a second per commit.
- **Tool-agnostic** — the same rules and knowledge are written out to Claude Code, Gemini, Cursor, and Copilot formats.

---

## What You Get After Install

### 1. Unified Codebase Intelligence Hub (`.wednesday/`)

- **AST-parsed Dependency Graph (`graph.db`)** — a SQLite database mapping every import, export, and function call across JS, TS, Python, Go, Swift, and more. This eliminates the need for AI agents to repeatedly read files, reducing LLM token consumption by up to 90%.
- **Living Agent Configuration Files** — automatically generates and updates `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, and `.github/copilot-instructions.md` with available skills and rules.

### 2. Guardrails and Enforced Quality Standards

- **Complexity Gates** — enforces a cyclomatic complexity limit of `8` before code is submitted.
- **Approved Design Assets** — enforces UI design rules, preventing developers or AI agents from creating custom/duplicate UI components and restricting imports to approved libraries (e.g. shadcn, Magic UI).
- **Git and Commit Discipline** — automatically installs git hooks:
  - `commit-msg`: enforces conventional commit formatting via `commitlint`.
  - `pre-commit`: requires a `@wednesday-skills:purpose` header on new files.
  - `post-commit` / `post-merge`: automatically maintains and syncs the dependency graph in `< 1s` using incremental updates.

### 3. Immediate Change & Impact Analysis

- **Blast Radius Analysis (`blast <file>`)** — instantly lists direct and transitive dependents that will be affected if a specific file or symbol changes.
- **Audit-ready Safety Scoring (`score <file>`)** — computes a detailed file risk score from `0` to `100` using import volume, public contracts, test coverage, and historical git bug history.

### 4. Automated Multi-Agent Architecture Planner (`plan`)

- **Parallel Synthesis** — triggers a parallel planning pipeline running PM, Architect, and Security personas simultaneously on any project brief. It resolves technical disagreements (tensions) and synthesizes a production-ready `PLAN.md` outlining phase breakdowns, auth strategies, and threat mitigations.

### 5. Seamless Skill Registry & Cost-Optimized Analytics

- **On-Demand Package Management** — use `ws-skills search` and `ws-skills add` to pull community-built skills. Enforces PR review fixes (`@agent fix all`) as atomic commits with zero friction.
- **Up to 99% Cost Savings** — structural queries are automatically routed to free-tier fallback models first, saving up to 99% on API bills (reducing a $0.1620 Sonnet analysis query down to $0.0013). Run `ws-skills stats --cost` to inspect monthly cost breakdowns locally.

---

## Why Use This

### For AI Agents

| Without this system | With this system |
|---------------------|-----------------|
| Reads 20 raw files to answer "what does auth do?" — ~6,000 tokens | Queries `graph.db` — 0 extra tokens |
| Guesses at dependency structure | BFS traversal on verified AST edges |
| Makes changes with no risk context | Checks blast radius before touching anything |
| Forgets conventions between sessions | Reads enforced rules from `CLAUDE.md` / `.cursorrules` on every turn |
| Produces inconsistent commit messages | Every commit enforced by `commit-msg` hook via commitlint |

### For Development Teams

- **Speed** — new AI agents (and new developers) are productive on day one. `MASTER.md` gives full architectural context without reading a single source file.
- **Safety** — high-risk files (risk score > 80) trigger a mandatory review pause before any AI is allowed to edit them. Blast radius is computed before the first keystroke.
- **Cost** — pre-computed graphs reduce LLM token spend on structural questions by 70–90%. Every `map` run prints a token usage report with savings.
- **Consistency** — all AI tools (Claude, Gemini, Cursor, Copilot) follow the same standards because they all read from the same source. No more "Claude lets me do X but Cursor blocks it."
- **Clean git history** — conventional commits enforced at the hook level. Every PR follows the same shape.

---

## Installation

**Requirements:** Node.js >= 18

```bash
# Option 1 — npx (no setup)
npx @wednesday-solutions-eng/ai-agent-skills install

# Option 2 — global
npm install -g @wednesday-solutions-eng/ai-agent-skills
wednesday-skills install

# Option 3 — shell (no npm)
bash install.sh
```

Run in your project root. The installer:

1. Copies skills into `.wednesday/skills/`
2. Writes `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, `.github/copilot-instructions.md`
3. Installs git hooks: `commit-msg`, `pre-commit`, `post-commit`, `post-merge`
4. Symlinks skills into `.claude/skills/` for Claude Code's skill picker
5. Writes `.commitlintrc.json` with GIT-OS conventional commit rules

**No API key is needed** when using skills inside Claude Code, Cursor, or Gemini CLI — the IDE is the intelligence engine and the skills are standard markdown instructions.

API keys are only needed for standalone CLI commands (`map`, `summarize`, `gen-tests`):

```bash
wednesday-skills config        # interactive setup wizard
```

Or add them to `.env` (see [Configuration](#configuration--environment-variables)).

---

## Quick Start

```bash
# 1. Install into your project
npx @wednesday-solutions-eng/ai-agent-skills install

# 2. (Optional) configure API keys for standalone CLI commands
wednesday-skills config

# 3. Build the full codebase graph
wednesday-skills map --full

# 4. Ask your AI anything — it now reads from graph.db
```

From here the graph auto-updates on every commit. You never need to run `map --full` again unless you want a full refresh.

---

## How to Use It

### Day 1 — Map the Codebase

```bash
wednesday-skills map --full
```

This runs the full pipeline: AST parse → dependency graph → module summaries → `MASTER.md`. On a 500-file codebase it takes ~2 minutes and costs under $0.01 using Gemini Flash-Lite.

### Every Day — Just Talk to the AI

Once mapped, your AI agent already knows the codebase. Open Claude Code or Cursor and ask naturally.

**Understanding the codebase:**

```
"Walk me through how a payment is processed."
"What does the auth middleware do?"
"Who owns the billing module?"
```

**Before making a change:**

```
"Is it safe to change the signToken function signature?"
"What breaks if I rename UserService?"
```

**Starting a new task:**

```
"Start ticket: Add rate limiting to the login endpoint."
→ Creates branch feat/rate-limiting-login
→ Prints PR description template
→ Enforces atomic commits throughout
```

**PR workflow:**

```
"@agent fix #2 and #4"          # fix specific review comments
"@agent fix all"                 # fix everything in the queue
```

**After shipping:**

```
"Run pre-deploy checklist for the auth service."
"Generate an onboarding guide for the payments module."
```

---

## Skills Reference

### Core Workflow

| Skill | Trigger | What happens |
|-------|---------|-------------|
| `wednesday-git` | Starting a task, committing, opening a PR | Enforces branch naming, atomic commits, conventional messages, GIT-OS PR format |
| `standards-kit` | Writing any code or UI | Blocks custom components, enforces complexity < 8, naming conventions, import ordering |
| `pr-review` | `@agent fix #N` in PR comments | Fetches comments, categorizes by impact, applies fixes as separate atomic commits |
| `deploy-checklist` | Pre/post deploy | Walks env vars, migrations, rollback plan, smoke tests, monitoring |
| `greenfield` | New project planning | Runs Architect + PM + Security personas in parallel, produces `PLAN.md` with tensions |

### Brownfield Intelligence

| Skill | Trigger | What happens |
|-------|---------|-------------|
| `codebase-intel` | Any structural question or pre-edit check | Queries `graph.db` for impact, risk score, blast radius, entry points, dead code |
| `brownfield-drift` | Architecture review or PR merge | Validates code boundaries against `PLAN.md` — blocks domain spillage |
| `brownfield-e2e-gen` | Test coverage gaps | Generates tests using real AST callers and mock behavior, not scaffolding |

A full list of bundled skills lives in [`skills/`](skills/) and the machine-readable index in [`registry/index.json`](registry/index.json).

---

## Common Scenarios

### Inheriting a Legacy Codebase

```
You: "Map this codebase completely."
```

AI runs `wednesday-skills map --full`. After ~2 minutes you have:

- `MASTER.md` — full architecture in plain English
- Risk scores on every file (0–100)
- Circular dependency report
- Dead code finder
- Module ownership from git blame

From this point, any structural question is answered from the graph, not from re-reading files.

### Fixing a Bug in a High-Risk Module

```
You: "Fix the token expiration bug in auth.ts."
```

The AI checks blast radius before writing a single line. If risk score > 80:

```
⚠ HIGH RISK — auth.ts has risk score 87
  This file is imported by 14 modules across Auth and Billing.
  Recommend running these 3 tests before editing: [list]
  Proceed? (y/n)
```

Only after your confirmation does it write code — then commits with `fix(auth): Resolve token expiry on silent refresh`.

### New Developer Onboarding

```
You: "Generate an onboarding guide for the payments module."
```

AI uses recursive SQL traversal on `graph.db` to trace the full request path from API entry point to database layer, producing a focused Mermaid diagram and file reading order — specific to the exact layer the developer needs to touch.

### Architecture Review Before a PR Merge

```
You: "Check if this PR follows our architecture."
```

`brownfield-drift` reads `PLAN.md` boundary rules and validates them against the actual import graph. If a frontend module starts importing from the database layer, it's caught here before merge.

---

## CLI Reference

The CLI is exposed as both `wednesday-skills` and `ws-skills`.

### Setup & Configuration

```bash
wednesday-skills install                   # install + configure all agents
wednesday-skills configure [dir] [agent]   # configure a specific agent
wednesday-skills config                    # interactive API key and model setup
wednesday-skills model                     # choose models per task class
wednesday-skills sync                      # re-sync all agent config files
wednesday-skills sync --tool cursor        # re-sync one specific agent
```

### Codebase Intelligence

```bash
wednesday-skills map --full                # full AST parse → graph.db → MASTER.md
wednesday-skills map --incremental         # update changed files only (< 1s)
wednesday-skills analyze                   # analyze structure and risk
wednesday-skills summarize                 # generate module summaries
wednesday-skills fill-gaps                 # fill missing summaries
wednesday-skills blast <file>              # blast radius report for a file
wednesday-skills score <file>              # risk score 0–100
wednesday-skills trace <symbol>            # trace a symbol through the graph
wednesday-skills symbols <file>            # list symbols in a file
wednesday-skills dead                      # dead files and unreferenced exports
wednesday-skills legacy                    # legacy/modernization report
wednesday-skills api-surface               # public API surface report
wednesday-skills chat "question"           # plain-English codebase Q&A
wednesday-skills onboard                   # onboarding guide for a module
wednesday-skills query <type> [args]       # direct graph query
```

### Planning, Refactoring & Testing

```bash
wednesday-skills plan [dir]                # greenfield parallel persona planning
wednesday-skills plan-refactor             # plan a refactor from the graph
wednesday-skills plan-migration            # plan a framework/language migration
wednesday-skills drift                     # architecture drift vs PLAN.md
wednesday-skills gen-tests                 # generate tests from real AST callers
```

### Git & PR

```bash
wednesday-skills pr                        # validate + push + open PR
wednesday-skills coverage                  # test coverage report
wednesday-skills sonar                     # SonarQube report
wednesday-skills dashboard                 # launch Ink TUI dashboard
wednesday-skills dashboard --pr <num>      # open a single PR in the dashboard
```

### Skill Registry

```bash
wednesday-skills list                      # installed skills
wednesday-skills search <term>             # search community registry
wednesday-skills add <skill>               # install from registry
wednesday-skills remove <skill>            # remove an installed skill
wednesday-skills update                    # update all skills
wednesday-skills check                     # validate installed skills
wednesday-skills build-skill               # scaffold a new skill
wednesday-skills submit                    # submit a skill to the registry
wednesday-skills stats                     # token usage and cost breakdown
```

### Help

```bash
wednesday-skills help
wednesday-skills --help
wednesday-skills -h
```

More detail lives in [`docs/cli-reference.md`](docs/cli-reference.md).

---

## Configuration & Environment Variables

Copy `.env.example` to `.env` and fill in what you need. `.env` is gitignored — never commit it.

```bash
# Option 1 — local .env (per project, loaded from cwd)
cp .env.example .env

# Option 2 — shell profile (global, all terminals)
export OPENROUTER_API_KEY=sk-or-...
export GITHUB_TOKEN=ghp_...

# Option 3 — global ~/.wednesday/.env (shared across projects)
```

| Variable | Required | Purpose |
|----------|----------|---------|
| `OPENROUTER_API_KEY` | Required for `plan` + `triage` | LLM access via OpenRouter (Gemini Flash-Lite default). Get one at https://openrouter.ai/keys |
| `ANTHROPIC_API_KEY` | Optional | Fallback LLM provider |
| `GITHUB_TOKEN` | Required for dashboard PR panel | Fine-grained token with `repo:read`, `pull_requests:read` |

For GitHub Actions (triage workflow), add these as repository secrets instead of files.

---

## Project Layout After Install

```
your-project/
├── CLAUDE.md                          ← Claude Code instructions (auto-managed)
├── GEMINI.md                          ← Gemini CLI instructions (auto-managed)
├── .cursorrules                       ← Cursor instructions (auto-managed)
├── .github/
│   └── copilot-instructions.md        ← Copilot instructions (auto-managed)
├── .commitlintrc.json                 ← Conventional commit rules
└── .wednesday/
    ├── tools.json                     ← Which agents are registered
    ├── config.json                    ← API keys and model preferences
    ├── skills/                        ← Installed skill SKILL.md files
    ├── graph.db                       ← SQLite dependency graph (auto-updated)
    └── codebase/
        ├── MASTER.md                  ← AI-generated architecture guide
        ├── summaries.json             ← Module purpose index
        └── dep-graph.json             ← Serialized graph for tooling
```

---

## Git Hooks

Installed into `.git/hooks/`:

| Hook | What it enforces |
|------|------------------|
| `commit-msg` | Conventional commit format via commitlint |
| `pre-commit` | `@wednesday-skills:purpose` header required on new JS/TS files |
| `post-commit` | Incremental graph update after every commit (< 1s) |
| `post-merge` | Incremental graph update after merges/pulls |

Commit format enforced:

```
type(scope): Description

# Examples
feat(auth): Add refresh token rotation
fix(dashboard): Resolve PR panel crash on empty list
chore(deps): Bump octokit to v20
```

---

## Repository Structure

```
ai-agent-skills/
├── bin/
│   └── cli.js                 # CLI entry point (wednesday-skills / ws-skills)
├── src/
│   ├── adapters/              # Agent config adapters (Claude, Gemini, Cursor, Copilot)
│   ├── brownfield/            # LLM client, graph queries, drift, test generation
│   └── dashboard/             # Ink-based terminal TUI dashboard
├── lib/                       # analytics, builder, composer, router
├── skills/                    # Bundled agent skills (SKILL.md each)
│   ├── codebase-intel/
│   ├── wednesday-git/
│   ├── standards-kit/
│   ├── pr-review/
│   ├── greenfield/
│   ├── deploy-checklist/
│   ├── brownfield-drift/
│   ├── brownfield-e2e-gen/
│   └── agents/
├── scripts/                   # plan.js, pr-create.js, triage.js, generate-registry.js
├── registry/index.json        # Machine-readable skill index
├── assets/                    # Static assets used by skills
├── docs/                      # Full documentation set
├── test/                      # Node.js built-in test suite
├── test-fixtures/             # Fixtures for tests
├── .claude/, .kilo/, .github/ # Agent/tooling configuration
├── .commitlintrc.json         # Conventional commit rules
├── .env.example               # Environment variable template
├── install.sh                 # Shell installer (no npm required)
├── CLAUDE.md                  # Project instructions for Claude Code
├── CASE_STUDY.md              # Real-world usage case study
├── LLM_INTELLIGENCE_REPORT.md # Token/cost analysis report
└── package.json               # npm manifest (bin: wednesday-skills, ws-skills)
```

---

## Development

```bash
# Clone your fork
git clone https://github.com/<your-user>/ai-agent-skills.git
cd ai-agent-skills

# Install dependencies
npm install

# Run the CLI locally
node bin/cli.js help
node bin/cli.js list

# Regenerate the skill registry index after adding/changing skills
node scripts/generate-registry.js
```

### Scripts

| Command | Description |
|---------|-------------|
| `npm test` | Run the test suite (`node --test test/**/*.test.js`) |

### Conventions

- **Complexity** — maximum cyclomatic complexity is `8`.
- **Naming** — `PascalCase` for components/types, `camelCase` for functions/variables, `UPPER_SNAKE_CASE` for constants.
- **Commits** — conventional commits, one logical change per commit (`type(scope): Description`).
- **No secrets** — never commit `.env` files, API keys, or tokens.

---

## Testing

Tests use the Node.js built-in test runner — no extra framework required.

```bash
npm test
```

Tests live in `test/` with shared fixtures in `test-fixtures/`. Edge-case scripts such as `test-edge-cases.js` can be run directly with Node:

```bash
node test-edge-cases.js
```

---

## Documentation

| Document | Contents |
|----------|----------|
| [`docs/getting-started.md`](docs/getting-started.md) | First-run walkthrough |
| [`docs/architecture.md`](docs/architecture.md) | System design and data flow |
| [`docs/cli-reference.md`](docs/cli-reference.md) | Every CLI command in detail |
| [`docs/skills-reference.md`](docs/skills-reference.md) | Every skill, trigger, and behavior |
| [`docs/best-practices.md`](docs/best-practices.md) | Recommended workflows |
| [`docs/token-cost-report.md`](docs/token-cost-report.md) | Token usage and cost analysis |
| [`CASE_STUDY.md`](CASE_STUDY.md) | Real-world case study |
| [`LLM_INTELLIGENCE_REPORT.md`](LLM_INTELLIGENCE_REPORT.md) | LLM intelligence report |

---

## Contributing

1. Fork the repository and create your branch: `git checkout -b feat/my-change`
2. Make your changes, keeping cyclomatic complexity ≤ 8.
3. Add or update tests in `test/`.
4. Run the suite: `npm test`
5. Commit using conventional format: `feat(skills): Add new skill`
6. Open a PR (maximum 6 files per PR — split larger changes).

Bug reports and feature requests are welcome via [GitHub Issues](../../issues).

---

## License

MIT © Wednesday Solutions — see [LICENSE](LICENSE) for details.
