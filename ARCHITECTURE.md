# Lobsternaut Architecture

## Domain Map

Lobsternaut is organized into 8 domains. Each domain has clear boundaries and dependency rules.

```
┌─────────────────────────────────────────────────────────────┐
│                     CONTENT PIPELINE                         │
│  Social media generation, analytics, self-improving skills   │
├─────────────────────────────────────────────────────────────┤
│                     WEB FRONTEND                             │
│  Orbit visualizer, conjunction analyzer, user dashboard      │
├──────────────────┬──────────────────┬───────────────────────┤
│   ACCESS CONTROL │   PAYMENT LAYER  │     TOKEN LAYER       │
│   Tiered gating  │   Stripe, CB     │  $CLAW multi-chain    │
├──────────────────┴──────────────────┴───────────────────────┤
│                     WASM BRIDGE                              │
│  Emscripten build, JS/TS bindings, @lobsternaut/orbpro npm     │
├─────────────────────────────────────────────────────────────┤
│                    ORBPRO ENGINE (C++)                       │
│  Propagation, Coordinates, Optimization, Conjunction,        │
│  Mission Analysis                                            │
├─────────────────────────────────────────────────────────────┤
│                     COMMUNITY                                │
│  Discord, governance, token-gating (Collab.Land)             │
└─────────────────────────────────────────────────────────────┘
```

## Dependency Rules

Dependencies flow **downward only**. No domain may depend on a domain above it.

- Content Pipeline → Web Frontend, Access Control (read-only metrics)
- Web Frontend → WASM Bridge, Access Control
- Access Control → Token Layer, Payment Layer
- Payment Layer → (external: Stripe, Coinbase Commerce)
- Token Layer → (external: Base, Solana, Ethereum RPCs)
- WASM Bridge → OrbPro Engine
- OrbPro Engine → (external: SOFA, Eigen, Boost)
- Community → Token Layer, Access Control

## Layering Within Each Domain

Each domain follows this internal layer structure (from Harness Engineering):

```
Types → Config → Repo → Service → Runtime → UI
```

- **Types**: Data shapes, interfaces, enums. No logic, no imports from other layers.
- **Config**: Environment config, feature flags, constants.
- **Repo**: Data access layer. Reads/writes external state (DB, API, filesystem).
- **Service**: Business logic. Orchestrates repos, enforces invariants.
- **Runtime**: Lifecycle management, scheduling, event handling.
- **UI**: Presentation layer (web components, CLI output, social media formatting).

Cross-cutting concerns (auth, telemetry, logging) enter through a **Providers** interface at the Service layer.

## OrbPro Engine Modules

| Module | Location | Purpose |
| --- | --- | --- |
| Propagation | `orbpro/src/propagation/` | SGP4/SDP4, numerical integrators, perturbation models |
| Coordinates | `orbpro/src/coordinates/` | Frame transforms, time systems, geodetic conversions |
| Optimization | `orbpro/src/optimization/` | Lambert, Hohmann, low-thrust trajectories |
| Conjunction | `orbpro/src/conjunction/` | CDM parsing, collision probability, maneuver planning |
| Mission Analysis | `orbpro/src/mission/` | Ground tracks, access windows, eclipse prediction |

## Technology Choices

Following "prefer boring technology" principle:

| Concern | Choice | Rationale |
| --- | --- | --- |
| Core compute | C++17/20 | Industry standard for astrodynamics, Emscripten-compatible |
| Build system | CMake 3.20+ | Universal C++ build, Emscripten toolchain support |
| WASM compiler | Emscripten | Mature, well-documented, good LLM training coverage |
| JS bindings | Embind | Official Emscripten binding layer |
| Package format | ES6 modules + CJS | Universal JS compatibility |
| Backend | Node.js/Express | Simple, well-understood, good agent legibility |
| Database | PostgreSQL | Boring, reliable, excellent tooling |
| Payments | Stripe + Coinbase Commerce | Industry standard, good docs |
| Testing | Google Test (C++), Jest (JS) | Widely adopted, good agent support |
| CI/CD | GitHub Actions | Integrated with repository |
| Token standards | ERC-20, Token-2022, SPL | Chain-native standards |

## File Organization

```
lobsternaut/                    # This repo — agent harness + docs + OrbPro engine source
├── AGENTS.md                   # Agent system map (inject into context)
├── ARCHITECTURE.md             # This file
├── STRATEGIC_PLAN.md           # Business strategy
├── orbpro/                     # OrbPro codebase (not a separate standalone library repo)
├── agents/                     # Agent definitions and skills
├── docs/                       # Knowledge base (system of record)
├── tasks/                      # Plans and lessons
└── x-links/                    # Reference materials
```
