# Lessons Learned

> Updated by all agents after every task. Captures what worked, what failed, and why.
> Stable patterns get promoted to skill files. This file is the staging area.

## Format

Use the structured handoff format from `agents/skills/shared/handoff-protocol.md` for all entries.
The handoff protocol supersedes this simpler format for task completions. Use the short format
below only for quick observations that are not tied to a specific task:

```markdown
### [YYYY-MM-DD] Short title (Agent)

**Outcome**: Success / Failure / Partial
**What happened**: Brief description
**Root cause** (failures only): Why it failed
**Rule added**: What skill file was updated, or "pending promotion"
**Lesson**: One-line takeaway
```

For task completions, use the full handoff format (status, summary, acceptance checked,
files changed, scope compliance, concerns, suggestions, metrics).

## Entries

### [2026-02-20] GitHub tracking baseline for strategic planning (PlanningAgent)

**Outcome**: Success
**What happened**: Added a dedicated GitHub-project-tracking skill, wired PlanningAgent to require issue + project board tracking for `STRATEGIC_PLAN.md` work, and added strategic issue templates.
**Rule added**: `agents/skills/github-project-tracking.md` + planning rule `R-013` in `agents/skills/planning.md`
**Lesson**: Strategic work moves faster when issue structure and board status rules are codified in agent skills, not left as informal process.

### [2026-02-21] Replaced tiered access with unified access model (PlanningAgent)

**Outcome**: Success
**What happened**: Removed Free/Bronze/Silver/Gold feature-tier language from the strategic plan and aligned core product specs/agent docs to a baseline-free + metered credits model.
**Rule added**: pending promotion
**Lesson**: Access-model changes should be applied to both strategy docs and canonical product specs in the same pass to avoid drift.

### [2026-02-21] Shifted monetization to first-party network commerce (PlanningAgent)

**Outcome**: Success
**What happened**: Updated strategy and Web3 guidance so Lobsternaut monetizes by running its own data products, service endpoints, and NFT storefront on the network under the same operator rules as anyone else.
**Rule added**: pending promotion
**Lesson**: Pricing-model decisions should be encoded as operator behavior rules, not only as marketing language, so implementation stays aligned.

### [2026-02-23] Reframed OrbPro plan around upstream source links (PlanningAgent)

**Outcome**: Success
**What happened**: Removed commitments to a separately maintained open-source OrbPro library, updated strategy/spec/access docs to baseline-free terminology, and added `docs/references/orbpro-upstream-sources.md` as the canonical upstream-source link registry.
**Rule added**: pending promotion
**Lesson**: When product direction changes, update strategy, spec, access model, and agent skill language together to prevent policy drift.

### [2026-02-23] Added Lobsternaut software strategy track (PlanningAgent)

**Outcome**: Success
**What happened**: Updated strategic/spec docs to include Lobsternaut software as a NanoClaw-compatible client with embedded SDN node, bring-your-own inference provider support, and SpaceAware AI subscription-gated MCP workflows replacing per-token AI gating.
**Rule added**: pending promotion
**Lesson**: When entitlement mechanics shift (token gating to subscription gating), document the runtime architecture and access rules together to avoid implementation ambiguity.
