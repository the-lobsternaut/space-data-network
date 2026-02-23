# SPEC — Technical Specification (Binding Contract)

> This is the binding technical contract for Lobsternaut. Agents treat this as authoritative.
> Business strategy lives in `STRATEGIC_PLAN.md`. This file defines what "done" looks like technically.

## Product Statement

Lobsternaut is a professional-grade astrodynamics toolkit that makes orbital mechanics accessible to everyone — from aerospace engineers to space enthusiasts. It combines the OrbPro C++ compute engine, WebAssembly compilation for browser-based use, multi-chain token economics ($CLAW), and an autonomous AI agent system.

**For**: Satellite operators, aerospace engineers, space enthusiasts, university researchers
**Core value**: Professional astrodynamics capabilities accessible via browser, no local installation required

## Success Criteria (Ranked)

1. **Working WASM build**: OrbPro compiles to WebAssembly and runs in the browser with correct numerical results
2. **Conjunction assessment**: Users can upload TLE data or CDMs and get collision probability analysis
3. **Token deployment**: $CLAW deployed on Base with liquidity and functional marketplace/governance utility
4. **Content pipeline**: Automated educational content generation across X, TikTok, LinkedIn, YouTube
5. **Payment integration**: Stripe and Coinbase Commerce accepting payments and gating features
6. **Community**: Discord with token-gated channels and active engagement

## Hard Limits

| Constraint | Limit |
|-----------|-------|
| Compute | Single Mac Mini (always-on) |
| Monthly budget | $70-110/month (see AUTONOMOUS_SETUP.md) |
| WASM bundle size | 500 KB core (gzipped), 2 MB full (gzipped) |
| API response time | < 2 seconds for basic queries, < 30 seconds for Monte Carlo |
| Token supply | 1,000,000,000 fixed (no mint function) |
| CI build time | < 10 minutes for full pipeline |

## Acceptance Tests

These are specific, verifiable conditions — not vague goals:

1. **WASM correctness**: OrbPro WASM output matches C++ native output within R-004 tolerances (see build-pipeline.md)
2. **Conjunction analysis**: Given a known CDM test case (Foster 1992), collision probability matches reference within 1% relative error
3. **Tier gating**: Explorer/Analyst/Operator/Mission/AI Enabled subscriptions unlock the correct tier capabilities; Free remains available without payment
4. **Stripe flow**: Complete checkout → webhook → subscription active → feature unlocked, end-to-end
5. **Content generation**: ContentAgent produces 3 platform-ready posts from a single conjunction event trigger
6. **Cross-link integrity**: Zero broken links across all `docs/` files
7. **Build pipeline**: Push to main → CI runs all checks → green in < 10 minutes

## Non-Negotiables

- No TODOs, FIXMEs, or placeholders in shipped code
- No `any` types in TypeScript
- Every numerical algorithm has a cited reference (R-003 in build-pipeline.md)
- Upstream open-source code used by OrbPro is linked and attributed in `docs/references/orbpro-upstream-sources.md`
- Every public API function has tests
- Validation tests use reference data from STK, GMAT, or published papers
- No secrets in the repository (R-001 in web3-integration.md)
- Stripe webhook signatures always verified
- Token balance checks use latest block, not cached values
- All wallet addresses validated before processing

## Architecture Constraints

- See `ARCHITECTURE.md` for full domain map and dependency rules
- Dependencies flow downward: Core → Bridge → Frontend → Application layers
- External APIs are wrapped (not called directly from business logic)
- WASM bindings generated from Embind (not hand-written)
- TypeScript definitions auto-generated from Embind metadata
- OrbPro is maintained as product code, not as a separately maintained open-source library distribution

## Dependency Philosophy

**Allowed** (stable, well-documented, good LLM training coverage):

- Eigen, Boost (minimal), IAU SOFA, nlohmann/json, Google Test
- OpenZeppelin (smart contracts)
- Stripe SDK, Coinbase Commerce SDK
- Emscripten, CMake

**Requires justification**: Any dependency not on the allowed list needs a design doc, license check, Emscripten compatibility assessment, and bundle size impact measurement.

**Banned**: Bleeding-edge frameworks, unmaintained libraries, GPL-licensed code in the OrbPro core codebase

## Scope Model

### Must-Have (MVP)

1. OrbPro core: orbit propagation (SGP4, numerical) + coordinate transforms
2. WASM compilation with working browser demo
3. Conjunction assessment (basic: CDM parsing + collision probability)
4. $CLAW token on Base with Uniswap liquidity
5. Stripe/Coinbase checkout and webhook flow for six tiers (AI Enabled is usage-based)
6. Tier-gating middleware enforcing Free/Explorer/Analyst/Operator/Mission/AI Enabled capabilities
7. Content pipeline generating X/Twitter posts

### Nice-to-Have (Post-MVP)

1. Monte Carlo collision analysis
2. Mission planning tools (Hohmann transfers, launch windows)
3. Solana and Ethereum token deployments
4. Coinbase Commerce crypto payments
5. TikTok slideshow automation
6. YouTube tutorial scripts
7. Discord token-gated channels

### Out of Scope

- Operational mission control software (OrbPro is for education and analysis)
- Financial advisory on token pricing
- Mobile native apps (web-only for now)
- Real-time tracking (batch analysis only)
- Custom satellite hardware integration

## Definition of Done (Project-Level)

The project is "done" for a given milestone when:

- [ ] All acceptance tests pass
- [ ] All non-negotiables are satisfied
- [ ] Documentation is complete and cross-linked (Quality Score: B or above for all domains)
- [ ] CI pipeline is green
- [ ] No high-severity open issues
- [ ] Lessons logged for all significant decisions
