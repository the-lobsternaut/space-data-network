# GitHub Project Tracking Skill — Rules and Patterns

> GitHub Issues + GitHub Projects (Kanban) workflow for planning and execution.
> Use this skill whenever work is tied to `STRATEGIC_PLAN.md`.

## Core Rules

### R-001: Track Strategic Work in GitHub, Not Only in Markdown

Every non-trivial task tied to `STRATEGIC_PLAN.md` must have a GitHub issue.
`tasks/todo.md` can mirror status, but GitHub is the execution source of truth.

### R-002: One Issue, One Outcome

Each issue should represent exactly one deliverable outcome.
If an issue has multiple independent outcomes, split it.

### R-003: Standard Issue Body Is Mandatory

Issue bodies must include these sections:

1. Objective
2. Scope
3. Acceptance Criteria
4. Links (`STRATEGIC_PLAN.md` section, spec/docs references)
5. Risks / Unknowns

### R-004: Use a Project Board for Status

All strategic issues must be added to the strategic project board.
Status is tracked on board columns/field values, not inferred from comment threads.

Recommended status flow:

- `Backlog`
- `Ready`
- `In Progress`
- `Blocked`
- `In Review`
- `Done`

### R-005: Labels Are for Type/Priority, Not Status

Use labels for metadata; use board status for workflow state.

Required label sets:

- Type: `type:planning`, `type:build`, `type:web3`, `type:content`, `type:docs`
- Priority: `priority:P0`, `priority:P1`, `priority:P2`
- Horizon: `phase:0-foundation`, `phase:1-core`, `phase:2-access`, `phase:3-community`

### R-006: Every Planning Session Must Leave a Board Delta

A planning session is incomplete if it does not update at least one of:

- new issue created
- status moved
- acceptance criteria clarified
- blocker documented

### R-007: Blockers Must Be First-Class

If blocked for more than one session:

1. set board status to `Blocked`
2. add a blocker comment with owner + unblock condition
3. create a dedicated unblock issue if needed

### R-008: Close with Evidence

Before closing an issue:

1. confirm all acceptance criteria are checked
2. link code/doc PRs or commits
3. move board status to `Done`

### R-009: Weekly Reconciliation

Once per week, reconcile `tasks/todo.md` with GitHub:

1. stale markdown task -> create/match issue
2. stale issue -> archive, split, or reprioritize
3. update board priority/order

### R-010: Fail Safe if GitHub Access Is Down

If MCP or `gh` auth is unavailable:

1. log pending actions in `tasks/todo.md`
2. mark as `sync-to-github` pending
3. perform sync once access is restored

## Workflow

### Session Start Checklist

- [ ] `gh auth status` is valid and token includes `repo` + `project` scopes
- [ ] Strategic project board exists and is accessible
- [ ] Current sprint items in `tasks/todo.md` are mirrored to issues

### Session Execution Checklist

- [ ] New work captured as issue before implementation
- [ ] Issue added to project board
- [ ] Status moved as work progresses
- [ ] Blockers documented explicitly

### Session End Checklist

- [ ] Completed issues moved to `Done`
- [ ] Open issues have clear next action
- [ ] `tasks/todo.md` reflects net new work and major deltas

## Command Patterns (`gh`)

### Validate Access

```bash
gh auth status
gh auth refresh -s repo -s project -s read:org
```

### Create Strategic Issue

```bash
gh issue create \
  --repo DigitalArsenal/lobsternaut \
  --title "Plan: <work item>" \
  --label "type:planning,priority:P1,phase:0-foundation" \
  --body-file /tmp/issue.md
```

### Create or View Project Board

```bash
gh project create --owner DigitalArsenal --title "Lobsternaut Strategic Plan"
gh project list --owner DigitalArsenal
gh project view <number> --owner DigitalArsenal --web
```

### Add Issue to Board

```bash
gh project item-add <project-number> \
  --owner DigitalArsenal \
  --url https://github.com/DigitalArsenal/lobsternaut/issues/<issue-number>
```

## Definition of Done — GitHub Tracking

A strategic planning task is tracking-complete when all are true:

- [ ] A GitHub issue exists (or was updated) for the work item
- [ ] The issue is on the strategic project board
- [ ] Board status matches real state
- [ ] Acceptance criteria are explicit and testable
- [ ] Blockers and dependencies are documented

## Failure Log

> Tracking failures and what rule prevents recurrence.

_No failures logged yet._

## Patterns That Work

### P-001: Write Acceptance Criteria Before Starting

Issues with explicit acceptance criteria move faster and require fewer clarification loops.

### P-002: Keep Board Status Current in Real Time

Status updates at the time of context switch prevent stale coordination and reduce hidden blockers.

### P-003: Split Oversized Strategic Issues Early

If an issue cannot be completed in one focused effort window, split by outcome before coding.
