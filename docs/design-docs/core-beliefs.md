# Core Beliefs — Agent-First Operating Principles

> These are the golden principles for the Lobsternaut agent system.
> Every agent, linter, and review process should enforce these.
> Derived from OpenAI Harness Engineering + Larry Pattern + team experience.

## 1. Repository Is the System of Record

If information isn't in the repository, it doesn't exist to agents. Slack discussions, verbal agreements, and external docs must be encoded into the repo as markdown before agents can act on them.

**Enforcement**: Agents should refuse to act on instructions that reference unversioned, external-only context. When a human mentions something from outside the repo, the first action is to write it down here.

## 2. Progressive Disclosure

`AGENTS.md` is the ~100-line table of contents. It points to deeper sources of truth in `docs/`. No single file should try to contain everything. Agents start with a small, stable entry point and follow links to go deeper.

**Enforcement**: AGENTS.md must stay under 120 lines. Any new documentation goes in `docs/`, not in AGENTS.md.

## 3. Plans Are First-Class Artifacts

Execution plans are not throwaway notes. They are versioned files in `docs/exec-plans/active/` with:
- Clear objective
- Steps with checkboxes
- Decision log (why we chose X over Y)
- Completion criteria

When done, move to `docs/exec-plans/completed/`. When blocked, document why in the plan itself.

## 4. Skills Compound

Every failure becomes a rule. Every success becomes a pattern. Skill files grow over time as agents encounter new situations. A skill file with 500 lines of hard-won rules is more valuable than a clean 20-line template.

**Process**:
- Agent encounters failure → writes rule to prevent recurrence
- Agent notices success pattern → writes rule to reinforce
- Nightly: review `agents/memory/MEMORY.md`, promote stable patterns to skill files

## 5. Validate at Boundaries

Parse and validate all external data at the system edge. Internal code trusts typed interfaces. External inputs (API responses, user data, CDM files, blockchain state) are validated on entry.

**Enforcement**: Every repo/service boundary has explicit input validation. No "YOLO-style" data probing.

## 6. Prefer Boring Technology

Choose composable, stable technologies with well-documented APIs and broad LLM training coverage. "Boring" technologies are easier for agents to model, debug, and maintain.

**Preferences**:
- PostgreSQL over novel databases
- Express over bleeding-edge frameworks
- CMake over custom build systems
- ERC-20 over exotic token standards
- Jest/Google Test over niche testing frameworks

**Exception**: When the boring option genuinely can't meet requirements (e.g., WASM compilation requires Emscripten).

## 7. Agent Legibility First

Code is optimized for agent comprehension, not human aesthetics. This means:
- Explicit over clever
- Flat over nested
- Named constants over magic numbers
- Descriptive variable names over terse ones
- Comments explain "why", code explains "what"

Human taste is captured in skill files and enforced via linters, not through manual code review.

## 8. Enforce Architecture Mechanically

Documentation alone doesn't keep a codebase coherent. Architecture rules must be enforced via:
- Custom linters (written by agents, for agents)
- Structural tests (dependency direction, layer violations)
- CI gates (build must pass before merge)
- Doc-gardening agent (scans for drift between code and docs)

If a rule matters, it has a test. If it doesn't have a test, it will eventually be violated.

## 9. Self-Improvement Is Non-Negotiable

Every agent session should leave the system slightly better than it found it. This means:
- Updating `tasks/lessons.md` after corrections
- Adding rules to skill files when encountering new edge cases
- Flagging documentation drift when noticed
- Moving stable patterns from memory to skills

## 10. Verify Before Declaring Done

Before marking any task complete, ask: "Would a staff engineer approve this?"

Checklist:
- [ ] Changes diff'd and reviewed
- [ ] Tests pass (or tests written if none existed)
- [ ] Documentation updated if behavior changed
- [ ] No new warnings or lint violations introduced
- [ ] Execution plan updated with outcome
