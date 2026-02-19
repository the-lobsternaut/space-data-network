# Content Generation Skill — Rules and Patterns

> Self-improving content rules for OpenClaw's astrodynamics marketing.
> Inspired by Larry pattern: every flop becomes a rule, every hit becomes a formula.
> ~200 lines initial seed, expected to grow to 500+ through performance tracking.

## Core Rules

### R-001: Hook Is Everything

The hook determines whether anyone sees the rest. Spend 80% of creative energy on hooks. A mediocre image with a great hook outperforms a stunning image with a weak hook every time.

Hook formula: **Curiosity + Stakes + Relatability**
- "NASA flagged 3 collision warnings today. Here's what that means for your GPS."
- "I showed a satellite operator what our AI found. They couldn't believe this was free."
- "This 2-ton piece of space junk is headed toward a $400M satellite. Let me show you the math."

NOT:
- "Here are today's conjunction events" (no curiosity, no stakes)
- "Check out our new feature" (nobody cares about your feature)
- "5 facts about orbital mechanics" (listicles are dead)

### R-002: Make It About Other People

Content about "I" or "we" underperforms. Content about other people's reactions outperforms.

- BAD: "I calculated the collision probability"
- GOOD: "I showed an aerospace engineer this collision probability and they said 'this changes everything'"
- BAD: "We released a new orbit visualizer"
- GOOD: "A university professor used our orbit visualizer in class today. Here's what happened."

### R-003: Storytelling Over Feature Lists

Captions tell stories. They never list features. They never say "Download now!" They naturally mention the product as part of the narrative.

- BAD: "OpenClaw features: conjunction analysis, orbit propagation, collision avoidance. Try it free!"
- GOOD: "I was curious if an old rocket body would hit a Starlink satellite next week. So I ran the numbers. The probability came back at 1 in 4,300. Here's what that means and why satellite operators are losing sleep over it."

### R-004: Platform-Specific Formatting

#### X/Twitter
- Max 280 chars for single posts, threads for depth
- Lead with the hook — if they don't read the first line, they don't read anything
- Use line breaks for readability (no walls of text)
- 1-3 hashtags max (#SpaceDebris #Astrodynamics #OpenClaw)
- Images/visualizations get 2-3x engagement over text-only
- Best times: 8-10 AM EST, 12-2 PM EST, 7-9 PM EST

#### TikTok Slideshows
- 6 portrait images (1024x1536)
- Text overlay on slide 1: font size = 6.5% of image height
- Text position: 30% from top (top 10% hidden by status bar, bottom 20% by TikTok UI)
- Line breaks every 4-6 words (prevents horizontal squashing)
- Full hook on slide 1 — NEVER split across slides
- Structure: Hook -> Problem -> Discovery -> Transform 1 -> Transform 2 -> CTA
- Post as draft, add trending audio manually

#### LinkedIn
- Professional tone, data-driven
- Longer form (800-1500 words for articles)
- Include charts, visualizations, data tables
- Target audience: satellite operators, aerospace engineers, mission planners
- No crypto/token talk on LinkedIn — focus on the technical value

#### YouTube
- Script structure: Hook (15 sec) -> Context (30 sec) -> Deep dive (3-8 min) -> CTA (15 sec)
- Thumbnails: high contrast, large text, expressive face or dramatic visualization
- Titles: curiosity gap + specific number ("The 3-Body Problem That Almost Destroyed a $400M Satellite")

### R-005: Educational Content Categories

Rotate through these to maintain variety:

1. **Orbital Mechanics 101** — "What keeps the ISS in orbit?", Kepler's laws explained simply
2. **Space Debris Crisis** — Current statistics, Kessler syndrome, historical events (Cosmos-Iridium)
3. **Conjunction Analysis** — How collision probability is calculated, real CDM examples
4. **Mission Design** — Hohmann transfers, launch windows, orbit maintenance
5. **Behind the Build** — OrbPro development updates, code walkthroughs, architecture decisions
6. **Industry News** — SpaceX launches, debris events, satellite operator challenges
7. **Token/Community** — $CLAW updates, governance proposals, community milestones

### R-006: Astrodynamics Facts Library

Pre-validated facts for content (all verified against NASA/ESA sources):

- ISS orbits at 7.66 km/s (27,576 km/h) at ~408 km altitude
- There are 36,500+ tracked debris objects > 10 cm in LEO (ESA 2025)
- A 1 cm debris object at orbital velocity has the energy of a hand grenade
- The 2009 Cosmos-Iridium collision created 2,300+ tracked fragments
- GPS satellites orbit at 20,200 km altitude in MEO (12-hour period)
- GEO is at exactly 35,786 km altitude (23-hour 56-minute period)
- Collision probability of 1 in 10,000 (10^-4) is the typical threshold for avoidance maneuvers
- The Space Surveillance Network tracks 48,000+ objects as of 2025
- Kessler Syndrome: cascading collisions making certain orbits unusable
- Lambert's problem: find the orbit connecting two points in a given time

### R-007: CTA Strategy

Never hard-sell. Always soft-mention. Rotate CTAs based on what's converting:

**CTA Templates**:
- "Built this with OpenClaw's conjunction analyzer. It's free and open source."
- "If you want to try this yourself, the code is on GitHub."
- "More on how this works in our docs at openclaw.ai"
- "Join our Discord if you want to geek out about orbital mechanics"
- "$CLAW holders get unlimited API access — just saying"

Track which CTAs convert. Drop those that don't after 2 weeks of testing.

### R-008: Volume Over Perfection

When starting on a new platform:
- Post 3x/day minimum
- Not every post will hit — that's fine
- 1 in 4 posts clearing 50K views is a success
- Let the algorithm learn your content profile
- Iterate fast: post, check performance, adjust, repeat

### R-009: Performance Tracking Template

After every post:

```
### [YYYY-MM-DD] [Platform] — [Hook summary]
**Views**: X
**Engagement**: likes/comments/shares
**Click-through**: X (if tracked)
**Conversions**: signups/holders (if attributable)
**Diagnosis**:
  - Views: High/Low
  - Conversions: High/Low
  - Action: Scale / Rotate CTA / Test new hook / Drop
**Hook style**: [curiosity/fear/surprise/educational/reaction]
```

### R-010: 2x2 Diagnostic Matrix

| | High Conversions | Low Conversions |
| --- | --- | --- |
| **High Views** | SCALE IT: Generate 3 variations of this hook | CTA problem: Keep hook, rotate CTA |
| **Low Views** | Hook problem: Keep CTA, test stronger hooks | DROP IT: Try radically different approach |

## Definition of Done — Content Tasks

A content task is complete when ALL of the following are verified:

- [ ] Hook formula applied (R-001): curiosity + stakes + relatability
- [ ] Content is about other people, not "I" or "we" (R-002)
- [ ] Storytelling format used, not feature lists (R-003)
- [ ] Platform-specific formatting correct (R-004): character limits, image specs, text positioning
- [ ] CTA included and rotated per current strategy (R-007)
- [ ] Educational content facts verified against R-006 library or cited source
- [ ] Performance tracking template prepared (R-009)
- [ ] Content queued as draft (not auto-published without review)
- [ ] Handoff produced per `agents/skills/shared/handoff-protocol.md`

For analytics review tasks specifically:

- [ ] 2x2 diagnostic matrix applied to each post (R-010)
- [ ] Underperformers diagnosed (hook problem vs CTA problem vs drop)
- [ ] Winners identified for scaling (generate 3 variations)
- [ ] Skill file updated with new patterns/failures
- [ ] Lessons logged to `tasks/lessons.md`

## Failure Log

> Hooks that flopped, CTAs that didn't convert, approaches that failed.

_No failures logged yet. Begin logging after first content posts._

## Success Patterns

> Hooks that hit, CTAs that converted, approaches that worked.

_No successes logged yet. Begin logging after first content posts._

## Hashtag Library

### Space/Astrodynamics
#SpaceDebris #Astrodynamics #OrbitalMechanics #SatelliteOps #SpaceSafety #ConjunctionAssessment #SpaceSustainability #LEO #GEO #SpaceTraffic

### Tech/AI
#OpenSource #AI #WebAssembly #WASM #CPlusPlus #SpaceTech #DeepTech

### Crypto/Web3 (X and Farcaster only, NOT LinkedIn)
#CLAW #Web3 #DeFi #Base #Solana #Ethereum

### Educational
#STEM #SpaceEducation #Physics #Engineering #AerospaceEngineering
