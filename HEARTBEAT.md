# HEARTBEAT — Periodic Wake-Up Instructions

> Tasks that run on a recurring schedule without explicit human triggering.
> The agent wakes up, performs the task, logs the outcome, and goes back to sleep.

## Heartbeat Schedule

### Every 4 Hours: Content Check

```
Trigger: Every 4 hours during active hours (8 AM - 10 PM EST)
Agent: ContentAgent
Actions:
  1. Check if scheduled posts need generating
  2. Check for breaking space news worth reacting to
  3. Check for conjunction alerts in public TLE data
  4. If content needed → generate, queue as draft
  5. Log activity to tasks/lessons.md
```

### Every 6 Hours: Memory Hygiene

```
Trigger: Every 6 hours
Agent: DocumentationAgent
Actions:
  1. Review agents/memory/MEMORY.md for observations to promote
  2. Check if any memory entries are > 1 week old and unconfirmed → remove
  3. Verify MEMORY.md hasn't been compacted away in active sessions
  4. Log any promotions to tasks/lessons.md
```

### Every 12 Hours: Build Health Check

```
Trigger: Every 12 hours
Agent: BuildAgent
Actions:
  1. Check CI/CD status — any failed builds?
  2. Check if WASM bundle size has changed significantly
  3. Check for dependency security advisories (npm audit, GitHub Dependabot)
  4. If issues found → attempt auto-fix or log to tasks/todo.md
  5. Update docs/QUALITY_SCORE.md if grades changed
```

### Daily: Performance Report (ContentAgent)

```
Trigger: 9 AM EST daily
Agent: ContentAgent
Actions:
  1. Pull last 24h of analytics from all platforms (via Postiz, X, YouTube)
  2. Run 2x2 diagnostic on each post (views vs conversions)
  3. Update agents/skills/content-generation.md with:
     - New patterns from high-performing posts
     - Failures from underperforming posts
  4. Generate daily summary
  5. Log to tasks/lessons.md
```

### Daily: Memory Review (DocumentationAgent)

```
Trigger: 11 PM EST daily (nightly)
Agent: DocumentationAgent
Actions:
  1. Full review of agents/memory/MEMORY.md
  2. Promote confirmed patterns (3+ interactions) to skill files
  3. Clean one-off observations older than 1 week
  4. Check for memory entries that contradict skill rules
  5. Review MEMORY.md for content that belongs in SKILLS or TOOLS instead
  6. Log all promotions and removals to tasks/lessons.md
```

### Weekly: Quality Audit (DocumentationAgent)

```
Trigger: Sunday 10 AM EST
Agent: DocumentationAgent
Actions:
  1. Full cross-link validation across docs/
  2. Drift detection: compare docs to actual code
  3. Update docs/QUALITY_SCORE.md grades for all domains
  4. Check docs/exec-plans/active/ for stale plans (no update in 2+ weeks)
  5. Open fix-up tasks in tasks/todo.md for any issues
  6. Log findings to tasks/lessons.md
```

### Weekly: Token & Revenue Metrics (Web3Agent)

```
Trigger: Monday 9 AM EST
Agent: Web3Agent
Actions:
  1. Pull token holder counts per chain
  2. Pull subscription counts and MRR from Stripe
  3. Pull Coinbase Commerce payment volume
  4. Check DEX liquidity pool health
  5. Log weekly snapshot to agents/memory/MEMORY.md
  6. If significant changes → notify ContentAgent for announcement
```

### Weekly: Skill File Review (PlanningAgent)

```
Trigger: Friday 3 PM EST
Agent: PlanningAgent
Actions:
  1. Review all skill files for rule conflicts or redundancy
  2. Check tasks/lessons.md for unprocessed entries
  3. Verify each lesson has been promoted to appropriate skill file
  4. Flag any skill files that haven't grown in 2+ weeks
  5. Report findings
```

## How Heartbeats Execute

On the Mac Mini, heartbeats are implemented as `launchd` scheduled jobs or cron entries:

```bash
# Example crontab entries
# Content check every 4 hours during active hours
0 8,12,16,20 * * * /path/to/openclaw/heartbeat.sh content-check

# Memory hygiene every 6 hours
0 0,6,12,18 * * * /path/to/openclaw/heartbeat.sh memory-hygiene

# Build health every 12 hours
0 6,18 * * * /path/to/openclaw/heartbeat.sh build-health

# Daily performance report at 9 AM EST
0 9 * * * /path/to/openclaw/heartbeat.sh daily-report

# Nightly memory review at 11 PM EST
0 23 * * * /path/to/openclaw/heartbeat.sh nightly-review

# Weekly quality audit Sunday 10 AM
0 10 * * 0 /path/to/openclaw/heartbeat.sh weekly-audit

# Weekly metrics Monday 9 AM
0 9 * * 1 /path/to/openclaw/heartbeat.sh weekly-metrics

# Weekly skill review Friday 3 PM
0 15 * * 5 /path/to/openclaw/heartbeat.sh weekly-skills
```

## Adding a New Heartbeat

1. Define the trigger (frequency and time)
2. Assign the owning agent
3. List the specific actions (numbered, concrete)
4. Add to this file
5. Add the cron entry to the Mac Mini
6. Test with a manual trigger first

## Heartbeat Log

> Track heartbeat execution history for debugging missed runs.

_No executions yet. Begin logging when cron jobs are configured._
