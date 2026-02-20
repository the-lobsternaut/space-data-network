# SKILLS — How to Do Things

> Unified index of all skill files. Each skill is a self-improving markdown file
> that grows as agents encounter new situations. Every failure becomes a rule,
> every success becomes a pattern.

## Skill Index

| Skill | File | Lines | Domain |
| --- | --- | --- | --- |
| Documentation | `agents/skills/documentation.md` | ~200 | Doc gardening, drift detection, quality scoring |
| Planning | `agents/skills/planning.md` | ~250 | Structured review, exec plans, task orchestration |
| Content Generation | `agents/skills/content-generation.md` | ~200 | Hooks, captions, TikTok/X/LinkedIn/YouTube |
| Build Pipeline | `agents/skills/build-pipeline.md` | ~200 | C++, CMake, Emscripten, WASM, CI/CD |
| Web3 Integration | `agents/skills/web3-integration.md` | ~200 | Tokens, Stripe, Coinbase, feature gating |
| Memory Management | `agents/skills/memory-management.md` | ~250 | Memory config, flush, retrieval, advanced tools |
| Code Review | `agents/skills/shared/code-review.md` | ~80 | Agent-to-agent review standards |
| Testing | `agents/skills/shared/testing.md` | ~100 | Test patterns across all domains |
| Handoff Protocol | `agents/skills/shared/handoff-protocol.md` | ~180 | Structured task results between agents |

## How Skills Work

Skills are **actionable rule sets**, not documentation. They contain:

1. **Numbered rules** (R-001, R-002...) — concrete instructions for specific situations
2. **Decision trees** — what to do given a specific trigger
3. **Failure logs** (F-001, F-002...) — what went wrong and the rule that prevents recurrence
4. **Success patterns** (P-001, P-002...) — what works and why

## How Skills Grow

```text
Agent encounters new situation
├── Success → Log to tasks/lessons.md → Add pattern (P-NNN) to skill file
├── Failure → Log to tasks/lessons.md → Add rule (R-NNN) to skill file
└── Uncertain → Log to agents/memory/MEMORY.md → Promote after 3 confirmations
```

Skills start at ~50-200 lines and grow to 500+ through real-world experience.
The Larry pattern (x-links/1): a skill file with 500 lines of hard-won rules is
more valuable than a clean 20-line template.

## Adding a New Skill

1. Create `agents/skills/<skill-name>.md`
2. Add it to this index
3. Reference it from the owning agent's definition file
4. Seed with initial rules from known requirements
5. Let it grow through the self-improvement loop
