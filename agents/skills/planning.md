# Planning Skill — Rules and Patterns

> Structured review workflow, execution planning, and task orchestration.
> Grows over time as the team learns what works. ~250 lines initial seed.

## Core Rules

### R-001: Plan Mode Is the Default

Enter plan mode for any task that requires 3+ steps or touches more than 2 files. Only skip for truly trivial changes (typo fixes, single-line changes, simple renames).

When in doubt, plan. The cost of planning is low. The cost of rework is high.

### R-002: Stop and Re-Plan When Things Go Wrong

If you're 3 steps into a plan and hit something unexpected:
1. Stop executing
2. Document what went wrong in the execution plan's decision log
3. Re-assess whether the plan is still valid
4. If not, create a revised plan before continuing
5. Never push through a plan that isn't working

### R-003: One Task Per Subagent

When delegating research or exploration to subagents:
- Give each subagent exactly one focused task
- Provide enough context for independent operation
- Don't duplicate work across subagents
- Collect results before proceeding with the main plan

### R-004: Verification Is Not Optional

Before marking any task done:
- [ ] Diff the changes — are they what you intended?
- [ ] Run the tests — do they pass?
- [ ] Check the docs — did behavior change that's documented?
- [ ] Ask: "Would a staff engineer approve this?"
- [ ] If any answer is "no" or "not sure", don't mark it done

### R-011: Enforce Completion Re-check Before Exit

When a task has multiple steps or open acceptance criteria, perform one explicit re-check pass before handing off.
If anything is still pending, continue execution even if the agent would normally stop.

Minimum re-check prompt:
- Are all requested outcomes complete?
- Are all required outputs produced (code/docs/notes)?
- Are errors and edge cases resolved or explicitly deferred?
- Are follow-on risks captured in `tasks/todo.md` or `tasks/lessons.md`?

### R-012: Taskmaster-Lite Manual Completion Gate (No Hooks Required)

Before ending any non-trivial task, run this manual gate in the final response:

1. Paste a quick status line for each objective: `done`, `partial`, or `blocked`.
2. Confirm all checks are answered `Yes`:
   - Diff matches requested scope and intent.
   - Acceptance criteria are checked and met or explicitly deferred.
   - Side effects or risks are logged in `tasks/todo.md` or `tasks/lessons.md`.
   - If there are unknowns, ask for user direction before marking the task complete.
3. If any item is not `done`, continue the task rather than closing it.

#### Required Final format for this repository

Use exactly these fields before handoff:

- `Completion Gate`: PASS / BLOCK
- `Outstanding Items`: `<brief list or 'none'>`
- `Deferral Notes`: `<brief or 'none'>`
- `Next Risk`: `<if BLOCK, describe one highest-priority risk>`

### R-013: Strategic Plan Work Requires GitHub Issue + Project Tracking

For any plan derived from `STRATEGIC_PLAN.md`:

1. Create/update a GitHub issue before execution
2. Place it on the strategic GitHub Project board
3. Use board status as the workflow state
4. Reflect important deltas back into `tasks/todo.md`

Follow `agents/skills/github-project-tracking.md` for detailed tracking rules and command patterns.

### R-005: Lessons Are Logged Immediately

Don't batch lessons. After every task completion (or failure):
1. Open `tasks/lessons.md`
2. Write the entry immediately
3. If a rule should be added to a skill file, do it now
4. Don't wait until "later" — later means never

### R-006: Execution Plans Have Decision Logs

Every execution plan in `docs/exec-plans/active/` must include a decision log table. When you make a choice between alternatives:

| Date | Decision | Rationale | Alternatives Considered |
| --- | --- | --- | --- |

This prevents rehashing decisions and gives future agents context on why things are the way they are.

### R-007: Number Issues, Letter Options

When presenting review findings:
- Issues are numbered: Issue 1, Issue 2, Issue 3
- Options within each issue are lettered: Option A (recommended), Option B, Option C
- Recommended option is always first (Option A)
- Each option includes: effort, risk, impact, maintenance burden
- Always include "do nothing" as an option when reasonable

### R-008: Review Stages Are Sequential

Architecture -> Code Quality -> Tests -> Performance. Always in this order. Pause after each stage for feedback. Don't skip stages even if they seem irrelevant — document "no issues found" and move on.

### R-009: Scope Creep Detection

If during execution you discover additional work:
1. Log it as a new task in `tasks/todo.md`
2. Do NOT fold it into the current task unless it's blocking
3. Finish the current task first
4. Then assess whether the new task needs its own plan

### R-010: Time-Box Research

When investigating an issue or exploring the codebase:
- Set a scope limit (e.g., "check these 5 files")
- If you haven't found what you need, escalate or ask the user
- Don't spiral into infinite exploration
- Document what you found even if it's incomplete

## Review Checklists

### Architecture Review Checklist

- [ ] Component boundaries are clear and documented
- [ ] Dependencies flow in one direction (see ARCHITECTURE.md)
- [ ] No circular dependencies
- [ ] External APIs are wrapped (not called directly from business logic)
- [ ] Authentication/authorization is at the boundary, not scattered
- [ ] Single points of failure identified and documented
- [ ] Data flow is traceable from input to output

### Code Quality Checklist

- [ ] No DRY violations (flag any repeated logic > 3 lines)
- [ ] Error handling covers all failure modes
- [ ] No magic numbers — use named constants
- [ ] Functions do one thing
- [ ] Module structure matches ARCHITECTURE.md layering
- [ ] No TODO/FIXME without a linked task in `tasks/todo.md`

### Test Checklist

- [ ] Every public function has at least one test
- [ ] Edge cases are tested (empty inputs, boundary values, error paths)
- [ ] Integration tests cover the critical path
- [ ] Test names describe the scenario, not the implementation
- [ ] No tests that depend on external state or ordering

### Performance Checklist

- [ ] No O(n^2) or worse in hot paths
- [ ] Database queries are indexed and bounded
- [ ] Large data sets are paginated or streamed
- [ ] Caching is used where appropriate (and invalidation is correct)
- [ ] Memory allocation patterns are reasonable (no leaks, no excessive copying)

## Execution Plan Templates

### Feature Addition

```markdown
# Execution Plan: Add [Feature Name]

**Created**: YYYY-MM-DD
**Status**: Active
**Owner**: [Agent]
**Review Mode**: BIG CHANGE / SMALL CHANGE

## Objective
[What and why]

## Pre-Conditions
- [ ] Design doc exists in docs/design-docs/
- [ ] Product spec exists in docs/product-specs/
- [ ] Architecture impact assessed

## Steps
- [ ] Implement core types and interfaces
- [ ] Implement business logic (service layer)
- [ ] Add unit tests
- [ ] Add integration tests
- [ ] Update documentation
- [ ] Update QUALITY_SCORE.md
- [ ] Run full test suite

## Decision Log
| Date | Decision | Rationale | Alternatives |
| --- | --- | --- | --- |

## Completion Criteria
- [ ] All tests pass
- [ ] Documentation complete
- [ ] Review approved
- [ ] Lessons logged
```

### Bug Fix

```markdown
# Execution Plan: Fix [Bug Description]

**Created**: YYYY-MM-DD
**Status**: Active
**Owner**: [Agent]

## Objective
[What's broken and user impact]

## Reproduction
[Steps to reproduce]

## Root Cause Analysis
[Why it's broken]

## Fix
- [ ] Implement fix
- [ ] Add regression test
- [ ] Verify fix doesn't break other tests
- [ ] Update docs if behavior description was wrong

## Decision Log
| Date | Decision | Rationale | Alternatives |
| --- | --- | --- | --- |
```

## Definition of Done — Planning Tasks

A planning task is complete when ALL of the following are verified:

- [ ] Execution plan written to `docs/exec-plans/active/` with all required sections
- [ ] All steps in the plan are actionable (specific files, specific changes, specific acceptance criteria)
- [ ] Decision log populated for every choice between alternatives (R-006)
- [ ] Review stages completed sequentially: Architecture → Code Quality → Tests → Performance (R-008)
- [ ] Issues numbered, options lettered, recommended option first (R-007)
- [ ] Scope creep detected and logged as separate tasks (R-009)
- [ ] "Would a staff engineer approve this plan?" — answer must be yes (R-004)
- [ ] Handoff produced per `agents/skills/shared/handoff-protocol.md`

For execution tracking tasks specifically:

- [ ] All plan steps have completion status (checked or unchecked)
- [ ] Completed plans moved to `docs/exec-plans/completed/`
- [ ] Lessons logged for every correction made during execution (R-005)
- [ ] Handoff concerns from other agents reviewed and tracked

## Failure Log

> Planning failures — when a plan went wrong and why.

_No failures logged yet._

## Patterns That Work

### P-001: Small PRs Over Large PRs

Break work into the smallest mergeable unit. A PR that does one thing is easier to review, easier to revert, and less likely to introduce regressions. If a plan has 10 steps, each step should ideally be its own PR.

### P-002: Design Doc First for Architectural Changes

Never start coding an architectural change without a design doc. The doc forces you to think through the implications, alternatives, and migration path. 30 minutes of writing saves days of rework.

### P-003: Prototype in a Branch, Then Rewrite Clean

For uncertain features, prototype quickly in a throwaway branch. Once the approach is validated, rewrite cleanly in a proper branch with tests and docs. The prototype teaches you what the clean version should look like.
