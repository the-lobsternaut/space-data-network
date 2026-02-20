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
