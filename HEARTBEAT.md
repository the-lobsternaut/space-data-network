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

On macOS, use native **LaunchAgents** instead of cron. LaunchAgents are built-in, more
reliable, survive reboots, and support richer trigger types than cron.

### launchd Trigger Types

| Trigger | Use When | Example |
| --- | --- | --- |
| `StartCalendarInterval` | Run at a specific time of day/week | Daily report at 9 AM |
| `StartInterval` | Run on a fixed interval (seconds) | Every 4 hours = 14400 seconds |
| `WatchPaths` | Run when a file/folder changes | Auto-push on save |
| `RunAtLoad` | Run once at login/boot | Agent startup |

### LaunchAgent Plists

All plists go in `~/Library/LaunchAgents/`. Load with `launchctl load <plist>`.

#### Content Check (every 4 hours)

File: `~/Library/LaunchAgents/com.lobsternaut.content-check.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.content-check</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh content-check</string>
    </array>
    <key>StartInterval</key>
    <integer>14400</integer>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-content-check.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-content-check.err</string>
</dict>
</plist>
```

#### Memory Hygiene (every 6 hours)

File: `~/Library/LaunchAgents/com.lobsternaut.memory-hygiene.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.memory-hygiene</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh memory-hygiene</string>
    </array>
    <key>StartInterval</key>
    <integer>21600</integer>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-memory-hygiene.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-memory-hygiene.err</string>
</dict>
</plist>
```

#### Build Health (every 12 hours)

File: `~/Library/LaunchAgents/com.lobsternaut.build-health.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.build-health</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh build-health</string>
    </array>
    <key>StartInterval</key>
    <integer>43200</integer>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-build-health.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-build-health.err</string>
</dict>
</plist>
```

#### Daily Performance Report (9 AM)

File: `~/Library/LaunchAgents/com.lobsternaut.daily-report.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.daily-report</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh daily-report</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>9</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-daily-report.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-daily-report.err</string>
</dict>
</plist>
```

#### Nightly Memory Review (11 PM)

File: `~/Library/LaunchAgents/com.lobsternaut.nightly-review.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.nightly-review</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh nightly-review</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>23</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-nightly-review.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-nightly-review.err</string>
</dict>
</plist>
```

#### Weekly Quality Audit (Sunday 10 AM)

File: `~/Library/LaunchAgents/com.lobsternaut.weekly-audit.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.weekly-audit</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh weekly-audit</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Weekday</key>
        <integer>0</integer>
        <key>Hour</key>
        <integer>10</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-weekly-audit.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-weekly-audit.err</string>
</dict>
</plist>
```

#### Weekly Token Metrics (Monday 9 AM)

File: `~/Library/LaunchAgents/com.lobsternaut.weekly-metrics.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.weekly-metrics</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh weekly-metrics</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Weekday</key>
        <integer>1</integer>
        <key>Hour</key>
        <integer>9</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-weekly-metrics.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-weekly-metrics.err</string>
</dict>
</plist>
```

#### Weekly Skill Review (Friday 3 PM)

File: `~/Library/LaunchAgents/com.lobsternaut.weekly-skills.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.weekly-skills</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh weekly-skills</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Weekday</key>
        <integer>5</integer>
        <key>Hour</key>
        <integer>15</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-weekly-skills.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-weekly-skills.err</string>
</dict>
</plist>
```

#### Auto-Push on File Changes (WatchPaths)

File: `~/Library/LaunchAgents/com.lobsternaut.auto-sync.plist`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.auto-sync</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/heartbeat.sh auto-sync</string>
    </array>
    <key>WatchPaths</key>
    <array>
        <string>/path/to/lobsternaut/agents/memory</string>
        <string>/path/to/lobsternaut/tasks</string>
    </array>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut-auto-sync.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut-auto-sync.err</string>
</dict>
</plist>
```

### Loading All LaunchAgents

```bash
# Load all Lobsternaut LaunchAgents
for plist in ~/Library/LaunchAgents/com.lobsternaut.*.plist; do
  launchctl load "$plist"
done

# Verify they are running
launchctl list | grep lobsternaut

# Unload a specific agent
launchctl unload ~/Library/LaunchAgents/com.lobsternaut.content-check.plist

# Check logs
tail -f /tmp/lobsternaut-*.log
```

### Why LaunchAgents Over Cron

- **Built into macOS** — no third-party scheduler needed
- **Survives reboots** — `RunAtLoad` triggers on login
- **Richer triggers** — `WatchPaths` fires on file changes (cron can't do this)
- **Catches up on missed runs** — if Mac was asleep, launchd runs it on wake
- **Per-job logging** — each plist has its own stdout/stderr paths
- **Easy management** — `launchctl load/unload` to start/stop

## Adaptive Frequency

> Adopted from agentswarm's Reconciler pattern. Fixed intervals miss urgent issues.
> Adaptive frequency responds fast to breakage and backs off when healthy.

### How It Works

Heartbeats that perform health checks (build-health, weekly-audit, nightly-review) support
adaptive frequency adjustment:

**On error detection**:
1. Log the error and classify by root cause
2. Emit fix tasks (max 5 per sweep) to `tasks/todo.md`
3. Schedule a follow-up check in 30 minutes using `launchctl kickstart`
4. Continue short-interval checks until resolved

**On consecutive greens (3+)**:
1. Return to the normal scheduled interval
2. Log "all green" to heartbeat log

**On persistent failure (3+ sweeps with same error)**:
1. Escalate to owner (Discord notification or email)
2. Do not keep emitting duplicate fix tasks
3. Log escalation to heartbeat log

### Implementation via launchctl kickstart

To trigger an immediate re-check after finding errors:

```bash
# Force an immediate run of a LaunchAgent (does not change the schedule)
launchctl kickstart gui/$(id -u)/com.lobsternaut.build-health

# The heartbeat.sh script should:
# 1. Run the sweep
# 2. If errors found: write a flag file /tmp/lobsternaut-<name>-recheck
# 3. A short-interval watcher plist monitors for the flag file (WatchPaths)
# 4. Watcher triggers a re-run, then removes the flag
```

### Applicable Heartbeats

| Heartbeat | Normal Interval | Error Re-check | Escalation Threshold |
|-----------|----------------|----------------|---------------------|
| build-health | 12 hours | 30 minutes | 3 consecutive failures |
| weekly-audit | 7 days | 2 hours | 3 consecutive failures |
| nightly-review | 24 hours | 4 hours | 3 consecutive failures |
| content-check | 4 hours | 1 hour | 5 consecutive failures |

Non-health-check heartbeats (daily-report, weekly-metrics, weekly-skills) run on fixed schedules
and do not use adaptive frequency.

## Adding a New Heartbeat

1. Define the trigger type (`StartInterval`, `StartCalendarInterval`, `WatchPaths`, or `RunAtLoad`)
2. Assign the owning agent
3. List the specific actions (numbered, concrete) in the schedule section above
4. Create the plist in `~/Library/LaunchAgents/com.lobsternaut.<name>.plist`
5. Load it: `launchctl load ~/Library/LaunchAgents/com.lobsternaut.<name>.plist`
6. Test with a manual trigger: `/path/to/lobsternaut/heartbeat.sh <name>`
7. Check logs: `tail /tmp/lobsternaut-<name>.log`

## Heartbeat Log

> Track heartbeat execution history for debugging missed runs.

_No executions yet. Begin logging when LaunchAgents are configured._
