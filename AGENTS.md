# OpenClaw Agent System

> This file is the map, not the encyclopedia. ~100 lines. Injected into every agent context.
> Deep documentation lives in `docs/`. Agent skills live in `agents/skills/`.

## Canonical Files

| File | Purpose |
| --- | --- |
| `SOUL.md` | Personality, behavior, and boundaries |
| `IDENTITY.md` | Agent name, role, and team structure |
| `USER.md` | Owner profile and preferences |
| `AGENTS.md` | This file — operating instructions and rules |
| `TOOLS.md` | → `agents/tools/TOOLS.md` — apps, APIs, services |
| `MEMORY.md` | → `agents/memory/MEMORY.md` — decisions, lessons |
| `SKILLS.md` | Skill index — how to do things |
| `HEARTBEAT.md` | Periodic wake-up tasks and schedules |
| `ARCHITECTURE.md` | Domain map and dependency rules |
| `SPEC.md` | Technical specification — binding contract |
| `DECISIONS.md` | Architecture decision log |
| `ENTRY_POINT.md` | Agent onboarding — read order and ownership |
| `STRATEGIC_PLAN.md` | Business strategy and roadmap |

## Quick Reference

- **Golden Principles**: `docs/design-docs/core-beliefs.md`
- **Active Tasks**: `tasks/todo.md`
- **Lessons Learned**: `tasks/lessons.md`
- **Quality Grades**: `docs/QUALITY_SCORE.md`
- **Autonomous Setup**: `docs/AUTONOMOUS_SETUP.md`

## Agents

| Agent | File | Skill File | Domain |
| --- | --- | --- | --- |
| Documentation | `agents/DocumentationAgent.md` | `agents/skills/documentation.md` | Docs, drift detection, gardening |
| Planning | `agents/PlanningAgent.md` | `agents/skills/planning.md` + `agents/skills/github-project-tracking.md` | Review, exec plans, task mgmt |
| Content | `agents/ContentAgent.md` | `agents/skills/content-generation.md` | Social media, educational content |
| Build | `agents/BuildAgent.md` | `agents/skills/build-pipeline.md` | C++ / WASM / CI/CD |
| Web3 | `agents/Web3Agent.md` | `agents/skills/web3-integration.md` | Tokens, payments, gating |

## Knowledge Base (`docs/`)

- `docs/design-docs/` — Architecture decisions, core beliefs, system designs
- `docs/exec-plans/` — Active and completed execution plans
- `docs/product-specs/` — Feature specifications
- `docs/references/` — API references, external docs
- `docs/generated/` — Auto-generated documentation

## Self-Improvement Loop

Every agent follows this cycle after each task:

1. Log outcome (success/failure) to `tasks/lessons.md`
2. If failure: analyze root cause, update relevant skill file with new rule
3. If success: note what worked for pattern reinforcement
4. Nightly: review `agents/memory/MEMORY.md`, promote stable patterns to skills

## Shared Resources

- `agents/skills/shared/code-review.md` — Agent-to-agent review standards
- `agents/skills/shared/testing.md` — Testing patterns across all domains
- `agents/skills/github-project-tracking.md` — GitHub Issues + Kanban board tracking for strategic work
- `agents/skills/shared/handoff-protocol.md` — Structured task result format
- `agents/skills/memory-management.md` — Memory config, failure prevention, advanced tools
- `agents/memory/MEMORY.md` — Cross-agent learnings
- `agents/tools/TOOLS.md` — Available tool configurations and API keys

## Repository Rules

1. Repository is the system of record — if it's not here, agents can't see it
2. This file is the map — deep docs go in `docs/`
3. Plans are artifacts — write them to `docs/exec-plans/`, track to completion
4. Skills compound — every failure becomes a rule, every success becomes a pattern
5. Validate at boundaries — parse all external data at the edge
6. Prefer boring technology — composable, stable, well-documented
7. Enforce architecture mechanically — linters and tests, not just docs
8. Agent legibility first — optimize for agent comprehension

## Domain Map

See `ARCHITECTURE.md` for full layering rules. Summary:

- **OrbPro Core** — C++ astrodynamics library (`../OrbPro`)
- **WASM Bridge** — Emscripten compilation, JS/TS bindings
- **Web Frontend** — Orbit visualizer, conjunction analyzer
- **Token Layer** — Multi-chain $CLAW (Base, Solana, Ethereum)
- **Payment Layer** — Stripe subscriptions, Coinbase Commerce
- **Access Control** — Tiered gating (token balance OR subscription)
- **Content Pipeline** — Social media generation and analytics
- **Community** — Discord, token-gating, governance
