# DECISIONS — Architecture Decision Log

> Every significant technical decision is logged here with rationale and alternatives.
> Agents reference this before proposing changes that might contradict prior decisions.
> Status: `active` (current), `replaced` (superseded by newer decision), `revisiting` (under review).

## Format

```
### [D-NNN] Decision Title
- **Date**: YYYY-MM-DD
- **Decision**: What was chosen
- **Why**: Rationale
- **Alternatives considered**: What else was evaluated
- **Status**: active | replaced | revisiting
```

## Decisions

### [D-001] LaunchAgents Over Cron for Heartbeats
- **Date**: 2026-02-17
- **Decision**: Use macOS LaunchAgents (launchd) instead of cron for all scheduled agent tasks
- **Why**: Built into macOS, survives reboots, supports WatchPaths (file change triggers), catches up on missed runs after sleep, per-job logging, richer trigger types
- **Alternatives considered**: cron (simpler syntax but fewer features, no file-watching, no catch-up)
- **Status**: active

### [D-002] Boring Technology Over Bleeding-Edge
- **Date**: 2026-02-17
- **Decision**: Prefer stable, well-documented technology with good LLM training coverage
- **Why**: Agents work better with technology well-represented in training data. Composable, stable APIs reduce debugging time. Less risk of hitting undocumented edge cases.
- **Alternatives considered**: Bleeding-edge frameworks (faster development velocity but higher failure rate, worse agent comprehension)
- **Status**: active

### [D-003] Separate Tokens Per Chain (No Bridging)
- **Date**: 2026-02-17
- **Decision**: Deploy separate $CLAW tokens on each chain (Base, Solana, Ethereum) rather than using cross-chain bridges
- **Why**: Simpler to deploy and manage, independent communities per ecosystem, no bridge security risks. Evaluate bridging based on demand at 1,000+ holders.
- **Alternatives considered**: Wormhole bridge (single token, complex, bridge hack risk), LayerZero OFT (newer, less battle-tested), single-chain only (limits reach)
- **Status**: active

### [D-004] Hybrid Access Model (Token OR Subscription)
- **Date**: 2026-02-17
- **Decision**: Users access premium features via EITHER holding $CLAW tokens OR paying a Stripe/Coinbase subscription. Higher tier wins.
- **Why**: Maximizes addressable market — crypto-native users buy tokens, traditional users pay subscriptions. Both paths lead to the same features.
- **Alternatives considered**: Token-only (excludes non-crypto users), subscription-only (no Web3 value prop), token discount on subscription (complex, confusing pricing)
- **Status**: active

### [D-005] Repository as System of Record
- **Date**: 2026-02-17
- **Decision**: All project knowledge lives in the repository as markdown. If it is not in the repo, it does not exist to agents.
- **Why**: Agents can only read the repo. External knowledge (Slack, email, meetings) is invisible. Markdown is universally parseable and versionable.
- **Alternatives considered**: External wiki (not accessible to agents), Notion (API complexity, not in git), Google Docs (not versionable)
- **Status**: active

### [D-006] Agent Skills as Self-Improving Markdown
- **Date**: 2026-02-17
- **Decision**: Each agent has a skill file (markdown) that grows over time. Every failure becomes a rule, every success becomes a pattern. Skills start at 200-300 lines and grow to 500+.
- **Why**: Adopted from the Larry pattern (x-links/1). Skills compound over time. A 500-line skill file with hard-won rules is more valuable than a clean 20-line template.
- **Alternatives considered**: Static instruction files (don't improve), database-backed rules (not in repo), code-based rules (less flexible)
- **Status**: active

### [D-007] Five Specialized Agents Over One General Agent
- **Date**: 2026-02-17
- **Decision**: Use 5 specialized agents (Documentation, Planning, Content, Build, Web3) rather than one general-purpose agent
- **Why**: Specialization allows deeper skill files per domain. Each agent has focused scope and can be invoked independently. Reduces context window pollution.
- **Alternatives considered**: Single agent (simpler but context gets diluted), 3 agents (too few for the domain spread), 10+ agents (too much coordination overhead at our scale)
- **Status**: active

### [D-008] Structured Handoff Protocol Between Agents
- **Date**: 2026-02-18
- **Decision**: Every agent task produces a structured handoff (status, summary, files changed, concerns, suggestions, metrics) rather than freeform log entries
- **Why**: Adopted from agentswarm pattern. Structured handoffs enable agents to consume each other's output programmatically. Concerns are tracked and don't disappear. Rich > sparse.
- **Alternatives considered**: Freeform lessons.md entries (not machine-parseable), JSON files (not human-readable), no inter-agent communication (each agent works blind)
- **Status**: active

### [D-009] Explicit File-Level Scope Per Agent
- **Date**: 2026-02-18
- **Decision**: Each agent has an explicit list of file path patterns it owns. Edits outside scope require justification in the handoff.
- **Why**: Adopted from agentswarm scope tracking. Prevents agents from stepping on each other. Makes code review easier (was this file supposed to be changed by this agent?).
- **Alternatives considered**: Prose-only domain descriptions (not enforceable), strict scope locking (too rigid, blocks legitimate cross-domain fixes), no scope (agents freely modify anything)
- **Status**: active

### [D-010] Self-Healing Reconciler Pattern
- **Date**: 2026-02-18
- **Decision**: BuildAgent and DocumentationAgent run reconciliation loops: sweep for issues → classify by root cause → emit targeted fix tasks → adaptive frequency
- **Why**: Adopted from agentswarm's Reconciler agent. Fixed-interval health checks miss issues (12h gap). Adaptive frequency responds fast to breakage and backs off when healthy.
- **Alternatives considered**: Manual-only issue detection (slow), continuous monitoring (expensive), fixed-interval only (misses urgent issues)
- **Status**: active
