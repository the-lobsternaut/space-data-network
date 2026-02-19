# Handoff Protocol — Structured Task Results

> Standard format for agents reporting task outcomes. Adopted from agentswarm's
> rich handoff pattern. Every task completion produces a handoff — not freeform prose,
> but a structured record that other agents can parse and act on.

## Why Structured Handoffs

Freeform logs in `tasks/lessons.md` are useful for humans but useless for agents:
- No machine-parseable structure
- No way for one agent to consume another's output programmatically
- No standard fields to check (did it succeed? what files changed? what concerns exist?)

The handoff protocol fixes this. Every task produces a handoff. Every handoff has the same fields.

## Handoff Format

```markdown
### [YYYY-MM-DD HH:MM] Task Title (AgentName)

**Status**: complete | partial | blocked | failed
**Summary**: 2-4 sentences describing what was done.
**Acceptance Checked**: [reference to Definition of Done criteria verified]

**Files Changed**:
- `path/to/file1.md` — what changed
- `path/to/file2.ts` — what changed

**Scope Compliance**: Yes | No (if No, justify why files outside scope were touched)

**Concerns**:
- [Risk, unexpected finding, or thing that worries you]
- [Cross-agent dependency discovered]
- [Technical debt introduced or discovered]

**Suggestions**:
- [Follow-up work discovered during this task]
- [Improvement idea for another agent's domain]

**Metrics**:
- Duration: Xm
- Tokens used: ~X
- Tool calls: X
```

## Field Definitions

### Status (required)

| Status | Meaning | Next Action |
|--------|---------|-------------|
| `complete` | Task fully done, acceptance criteria met | Log to lessons, close task |
| `partial` | Some work done, but not fully complete | Document what's left, create follow-up task |
| `blocked` | Cannot proceed — dependency or unclear requirement | Escalate to PlanningAgent or owner |
| `failed` | Attempted and could not succeed | Log root cause, create fix task or escalate |

### Summary (required)

2-4 sentences. What you did, not what you plan to do. Past tense.

**Good**: "Updated 3 design docs to match current API surface. Fixed 2 broken cross-links in docs/design-docs/. Promoted 1 memory entry to documentation.md skill file."

**Bad**: "Worked on docs." / "Did some updates."

### Acceptance Checked (required)

Reference the specific Definition of Done criteria from the relevant skill file. For example:
- "Checked: no broken links, examples compile, cross-references verified (documentation.md DoD)"
- "Checked: compiles, tests pass, bundle within size budget (build-pipeline.md DoD)"

### Files Changed (required)

List every file modified, created, or deleted. Include a brief note about what changed in each.
Files must be within the agent's declared scope (see agent definition). If a file outside scope
was touched, explain why under Scope Compliance.

### Scope Compliance (required)

`Yes` if all changed files are within the agent's declared scope.
`No` with justification if scope was exceeded. Scope violations are not always wrong — sometimes
a fix requires touching another agent's files — but they must be explicit and justified.

### Concerns (required — empty is suspicious)

Rich handoffs always have concerns. If you have zero concerns after a non-trivial task, you
probably didn't look hard enough. Common concerns:

- "File X has grown to 400 lines and should be split"
- "Found a cross-link to a doc that doesn't exist yet"
- "This approach works but may not scale past 1000 entries"
- "Discovered a contradiction between ARCHITECTURE.md and actual code"
- "This change may affect Web3Agent's payment flow — needs review"

### Suggestions (optional but encouraged)

Follow-up work discovered during this task. These become candidates for new tasks:
- "ContentAgent should announce this new feature"
- "BuildAgent should add a CI check for this new rule"
- "MEMORY.md has 3 entries ready for promotion"

### Metrics (optional)

Rough estimates are fine. Helps track efficiency over time.

## Rules

### R-001: Every Task Gets a Handoff

No exceptions. Even trivial tasks produce a handoff (abbreviated is fine).
The handoff is the only mechanism for information to flow between agents.

### R-002: Rich Over Sparse

An empty concerns field is a red flag. A one-sentence summary is a red flag.
If the task was non-trivial, the handoff should reflect that complexity.

### R-003: No Silent Deviations

If the task description said approach X but you used approach Y, the handoff
must explain why. Deviations are fine — undocumented deviations are not.

### R-004: Concerns Are Tracked

PlanningAgent reviews handoff concerns. A concern raised in sprint 3 that's
still unresolved by sprint 5 is a planning failure. Concerns don't disappear —
they're either resolved, converted to tasks, or explicitly accepted as known risks.

### R-005: Handoffs Go to lessons.md

Every handoff is logged to `tasks/lessons.md` using the format above.
The lessons.md file is the chronological record; skill files are the distilled rules.

### R-006: Blocked Handoffs Trigger Escalation

A `blocked` status handoff immediately notifies PlanningAgent. The blocking
reason must be specific enough for PlanningAgent to unblock without asking
the original agent for more context.

## Examples

### Minimal Handoff (trivial task)

```markdown
### [2026-02-18 14:00] Fix broken link in wasm-pipeline.md (DocumentationAgent)

**Status**: complete
**Summary**: Fixed relative link to orbpro-architecture.md that was pointing to wrong directory.
**Acceptance Checked**: All links valid (documentation.md DoD)

**Files Changed**:
- `docs/design-docs/wasm-pipeline.md` — corrected link path

**Scope Compliance**: Yes

**Concerns**:
- None (single link fix)

**Suggestions**:
- Consider adding a CI link-checker to catch these automatically
```

### Rich Handoff (non-trivial task)

```markdown
### [2026-02-18 16:30] Update API reference for Conjunction module (DocumentationAgent)

**Status**: complete
**Summary**: Regenerated API reference for the Conjunction module after new probabilityOfCollision() overload was added. Updated 4 doc files. Found and fixed 2 stale examples that referenced the old API signature.
**Acceptance Checked**: API docs match code, examples compile, cross-refs valid (documentation.md DoD)

**Files Changed**:
- `docs/references/orbpro-api-reference.md` — added new overload docs
- `docs/design-docs/orbpro-architecture.md` — updated Conjunction module description
- `docs/product-specs/conjunction-assessment.md` — updated feature description
- `docs/generated/conjunction-types.md` — regenerated from Doxygen

**Scope Compliance**: Yes

**Concerns**:
- The probabilityOfCollision() function now has 3 overloads. Users may find the API confusing. Consider a builder pattern or options struct to simplify.
- The Monte Carlo overload has no performance documentation. BuildAgent should benchmark and document expected runtime for 10K/100K/1M samples.
- Found that wasm-bindings.md doesn't include the new overload yet. BuildAgent needs to regenerate.

**Suggestions**:
- ContentAgent: This new Monte Carlo collision analysis is great content material. "We can now run 1M collision simulations in your browser."
- Web3Agent: The Monte Carlo feature is Gold-tier only. Verify the feature gating check covers the new endpoint.

**Metrics**:
- Duration: 25m
- Tokens used: ~12,000
- Tool calls: 18
```

### Blocked Handoff

```markdown
### [2026-02-18 10:00] Deploy $CLAW token to Base Sepolia testnet (Web3Agent)

**Status**: blocked
**Summary**: Attempted testnet deployment but cannot proceed. Deployer wallet has no Sepolia ETH for gas fees.
**Acceptance Checked**: N/A (could not execute)

**Files Changed**:
- None

**Scope Compliance**: N/A

**Concerns**:
- Need testnet ETH from a faucet. Base Sepolia faucets have been unreliable this week.
- Deployment script is ready and tested locally with Hardhat.

**Suggestions**:
- Owner: Request testnet ETH from Coinbase faucet (requires Coinbase account verification)
- Alternative: Deploy to Hardhat local node first to validate the full flow

**Metrics**:
- Duration: 15m
- Tokens used: ~3,000
- Tool calls: 8
```
