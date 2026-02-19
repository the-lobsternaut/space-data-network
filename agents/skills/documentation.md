# Documentation Skill — Rules and Patterns

> This file grows over time. Every documentation failure becomes a rule.
> Every success becomes a pattern. Started at ~200 lines, expected to reach 500+.

## Core Rules

### R-001: Repository Is the Only Source of Truth

If information exists only in Slack, email, or someone's head, it doesn't exist to agents. When you encounter references to external-only context, your first action is to write it into the repo as markdown.

- Always ask: "Is this written down in `docs/`?"
- If not, write it before proceeding with any other task
- External links are acceptable as references, but the key information must be summarized in-repo

### R-002: AGENTS.md Is the Map, Not the Encyclopedia

AGENTS.md must stay under 120 lines. It is a table of contents with links to deeper docs. If you're tempted to add detailed instructions to AGENTS.md, put them in the appropriate `docs/` file instead and link to it.

### R-003: Every Doc Needs an Index Entry

Every markdown file in a `docs/` subdirectory must be listed in that directory's `index.md`. Orphaned docs are invisible docs. When creating a new doc:

1. Write the doc
2. Add it to the directory's `index.md`
3. Add cross-links from related docs
4. Verify all links resolve

### R-004: Design Docs Have Status

Every design doc must have a status in the index:
- **Active**: Current and authoritative
- **Draft**: In progress, not yet reviewed
- **Superseded**: Replaced by newer doc (must link to replacement)
- **Archived**: No longer relevant but kept for history

### R-005: Document "Why", Not Just "What"

Code explains what. Comments explain why. Docs explain the decision. Every design doc should capture:
- What alternatives were considered
- Why this approach was chosen
- What tradeoffs were accepted
- Under what conditions we'd revisit the decision

### R-006: Drift Detection Patterns

Check for drift in this order (most impactful first):
1. API surface — do the documented endpoints/functions match the code?
2. Architecture — do the documented dependency rules hold?
3. Configuration — do documented env vars / build flags match reality?
4. Feature descriptions — do product specs match implemented behavior?
5. Tutorials — do code examples still compile and run?

### R-007: Cross-Link Validation

Every doc should link to:
- Its parent index
- Related docs in other directories (e.g., design doc links to product spec)
- The source code it describes (when applicable)

Broken links are bugs. Validate by checking every `[text](path)` pattern resolves.

### R-008: Generated Docs Go in `docs/generated/`

Auto-generated documentation (Doxygen output, API schemas, TypeScript definitions) goes in `docs/generated/`, never in hand-written doc directories. This prevents agents from accidentally editing generated content.

### R-009: Quality Score Grading Criteria

| Grade | Criteria |
| --- | --- |
| A | All public interfaces documented, all links valid, examples compile, updated within 2 weeks |
| B | Public interfaces documented with minor gaps, links valid, may have stale examples |
| C | Partial documentation, some broken links, no examples or stale examples |
| D | Minimal docs, significant gaps, broken links common |
| F | No documentation or severely stale (3+ months without update) |

### R-010: Memory Hygiene Protocol (Nightly)

1. Open `agents/memory/MEMORY.md`
2. For each entry:
   - Confirmed across 3+ interactions? -> Promote to appropriate skill file
   - Contradicts an existing rule? -> Investigate, update the correct one
   - One-off observation? -> Keep for one more week, then remove if not confirmed
   - Belongs in a different file (skill vs tool)? -> Move it
3. Log what was promoted/removed to `tasks/lessons.md`

## Astrodynamics Documentation Standards

### R-011: Technical Accuracy for Orbital Mechanics

When documenting astrodynamics features:
- Always specify the reference frame (ECI, ECEF, J2000, GCRF, etc.)
- Always specify the time system (UTC, UT1, TAI, TT, GPS)
- Include units on all numerical values (km, m/s, radians, degrees)
- Cite the algorithm source (author, year, paper title or DOI)
- Note validation status (validated against STK/GMAT/OREKIT or not yet)

### R-012: CDM (Conjunction Data Message) Documentation

CDM-related documentation must include:
- CCSDS format version referenced
- Fields parsed and their units
- Collision probability method used (Foster 1992, Patera 2005, Alfriend, etc.)
- Assumptions stated (e.g., linear covariance propagation, Gaussian distributions)
- Known limitations documented

### R-013: API Reference Standards

Every OrbPro public API function must document:
- Input parameters with types and units
- Return type with units
- Exceptions/errors that can be thrown
- Example usage (must compile and produce correct output)
- Performance characteristics (time complexity, memory usage for large inputs)
- Thread safety status

## Definition of Done — Documentation Tasks

A documentation task is complete when ALL of the following are verified:

- [ ] No broken cross-links (every `[text](path)` resolves to a real file)
- [ ] New docs are listed in their directory's `index.md`
- [ ] Code examples compile and produce correct output (or are marked as pseudocode)
- [ ] Technical claims are verified against actual code (no drift)
- [ ] Reference frames, time systems, and units are specified (R-011)
- [ ] Design docs have status (Active/Draft/Superseded/Archived) per R-004
- [ ] Quality score updated in `docs/QUALITY_SCORE.md` if grades changed
- [ ] No orphaned docs (every doc referenced from at least one other doc)
- [ ] Handoff produced per `agents/skills/shared/handoff-protocol.md`

For memory hygiene tasks specifically:
- [ ] Patterns confirmed 3+ times promoted to skill files
- [ ] One-off observations older than 1 week removed
- [ ] No memory entries contradicting existing skill rules
- [ ] Promotions and removals logged to `tasks/lessons.md`

## Failure Log

> Document every documentation failure here so we don't repeat it.

### Template

```
### [F-NNN] Short title
**Date**: YYYY-MM-DD
**What happened**: Description
**Root cause**: Why the docs were wrong
**Fix applied**: What was corrected
**Rule added/updated**: R-NNN
```

_No failures logged yet. Begin logging as documentation issues are discovered._

## Patterns That Work

### P-001: Write the Doc Before the Code

When planning a new feature, write the product spec first. This forces clarity on requirements and gives all agents a shared reference point. The spec can be wrong — it just needs to exist so we can iterate on it.

### P-002: Use Tables for Feature Matrices

Tiered features, platform-specific behavior, and comparison data are always clearer as tables than as prose. Use markdown tables liberally.

### P-003: One Concept Per Document

Don't combine unrelated topics in a single doc. "Token Strategy" and "Payment Integration" are separate docs even though they're related. Cross-link them instead.

### P-004: Progressive Disclosure in Tutorials

Start with the simplest possible example. Then add complexity. Never front-load all the options and configuration. The reader should be able to copy-paste the first example and get a working result.
