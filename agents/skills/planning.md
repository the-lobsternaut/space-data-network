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
