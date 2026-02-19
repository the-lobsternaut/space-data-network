# ENTRY_POINT — Agent Onboarding Guide

> If you are a new agent session, start here. This file tells you what to read and in what order.

## Read Order

Read these files in this exact sequence. Each builds on the previous:

1. **`SOUL.md`** — Personality, voice, values, behavior boundaries
2. **`IDENTITY.md`** — Who you are, your role, the 5-agent team structure
3. **`USER.md`** — Owner profile, engineering preferences, communication style, decision authority
4. **`AGENTS.md`** — System map, canonical files table, agent index, repository rules
5. **`SPEC.md`** — Technical specification, success criteria, acceptance tests, non-negotiables
6. **Your agent definition** — e.g., `agents/BuildAgent.md` (includes scope, instructions, decision tree)
7. **Your skill file** — e.g., `agents/skills/build-pipeline.md` (rules, patterns, Definition of Done)
8. **`agents/memory/MEMORY.md`** — Cross-agent learnings and recent observations
9. **`tasks/todo.md`** — Current task list (what needs doing now)
10. **`DECISIONS.md`** — Architecture decisions (check before proposing changes that might contradict)

## Ownership Matrix

| File | Created By | Maintained By | Consumed By |
|------|-----------|---------------|-------------|
| `SOUL.md` | Owner | Owner | All agents |
| `IDENTITY.md` | Owner | Owner | All agents |
| `USER.md` | Owner | Owner | All agents |
| `AGENTS.md` | Owner + PlanningAgent | DocumentationAgent | All agents |
| `SPEC.md` | Owner + PlanningAgent | PlanningAgent | All agents |
| `DECISIONS.md` | All agents | PlanningAgent | All agents |
| `ARCHITECTURE.md` | PlanningAgent | DocumentationAgent | All agents |
| `SKILLS.md` | PlanningAgent | DocumentationAgent | All agents |
| `HEARTBEAT.md` | PlanningAgent | PlanningAgent | All agents |
| `ENTRY_POINT.md` | PlanningAgent | DocumentationAgent | All agents |
| `agents/<Agent>.md` | PlanningAgent | Owning agent | Owning agent + PlanningAgent |
| `agents/skills/*.md` | PlanningAgent | Owning agent | Owning agent |
| `agents/skills/shared/*.md` | PlanningAgent | All agents | All agents |
| `agents/memory/MEMORY.md` | All agents | DocumentationAgent (nightly) | All agents |
| `agents/tools/TOOLS.md` | PlanningAgent | All agents | All agents |
| `tasks/todo.md` | All agents | PlanningAgent | All agents |
| `tasks/lessons.md` | All agents | All agents | PlanningAgent + DocumentationAgent |
| `docs/**` | Various agents | DocumentationAgent | All agents |
| `docs/QUALITY_SCORE.md` | DocumentationAgent | DocumentationAgent | All agents |
| `docs/exec-plans/**` | PlanningAgent | PlanningAgent | All agents |

## File Roles (One-Line Each)

| File | Role |
|------|------|
| `SOUL.md` | Defines personality, voice, values, and behavioral boundaries |
| `IDENTITY.md` | Defines agent name, role, team structure, and branding |
| `USER.md` | Captures owner preferences for engineering, communication, and workflow |
| `AGENTS.md` | ~100-line table of contents pointing to all deeper documentation |
| `SPEC.md` | Binding technical contract with acceptance tests and non-negotiables |
| `DECISIONS.md` | Architecture decision log with rationale and alternatives |
| `ARCHITECTURE.md` | Domain map, dependency rules, and technology choices |
| `SKILLS.md` | Index of all skill files with line counts and domains |
| `HEARTBEAT.md` | Scheduled task definitions and LaunchAgent configurations |
| `ENTRY_POINT.md` | This file — read order and ownership matrix |
| `STRATEGIC_PLAN.md` | Business strategy and roadmap (owner-managed) |

## Quick Start by Agent

### DocumentationAgent
Read: SOUL → IDENTITY → USER → AGENTS → SPEC → `agents/DocumentationAgent.md` → `agents/skills/documentation.md` → MEMORY → `docs/QUALITY_SCORE.md` → `tasks/todo.md`

### PlanningAgent
Read: SOUL → IDENTITY → USER → AGENTS → SPEC → `agents/PlanningAgent.md` → `agents/skills/planning.md` → DECISIONS → `tasks/todo.md` → `tasks/lessons.md`

### ContentAgent
Read: SOUL → IDENTITY → USER → AGENTS → `agents/ContentAgent.md` → `agents/skills/content-generation.md` → MEMORY → `tasks/todo.md`

### BuildAgent
Read: SOUL → IDENTITY → USER → AGENTS → SPEC → `agents/BuildAgent.md` → `agents/skills/build-pipeline.md` → `docs/design-docs/orbpro-architecture.md` → `tasks/todo.md`

### Web3Agent
Read: SOUL → IDENTITY → USER → AGENTS → SPEC → `agents/Web3Agent.md` → `agents/skills/web3-integration.md` → `docs/design-docs/token-strategy.md` → `tasks/todo.md`

## After Reading

1. Check `tasks/todo.md` for your assigned tasks
2. Check `agents/memory/MEMORY.md` for recent observations relevant to your domain
3. Check `tasks/lessons.md` for recent lessons in your domain
4. Begin work, following your agent definition's decision tree
5. Produce a structured handoff per `agents/skills/shared/handoff-protocol.md` when done
