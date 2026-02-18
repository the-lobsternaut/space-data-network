# Design Doc: OrbPro C++ Library Architecture

**Status**: Active
**Owner**: BuildAgent
**Last Updated**: 2026-02-18

## Overview

OrbPro is the core computational engine — a modular C++ astrodynamics library in the `../OrbPro` directory. It compiles natively for server use and to WebAssembly for browser-native performance.

## Module Map

| Module | Namespace | Purpose | Dependencies |
| --- | --- | --- | --- |
| Propagation | `orbpro::propagation` | Orbit propagation (Kepler, SGP4, numerical) | Coordinates |
| Coordinates | `orbpro::coordinates` | Frame transforms, time systems | (none — leaf module) |
| Optimization | `orbpro::optimization` | Lambert, Hohmann, trajectory design | Propagation, Coordinates |
| Conjunction | `orbpro::conjunction` | CDM, collision probability, avoidance | Propagation, Coordinates, Optimization |
| Mission | `orbpro::mission` | Ground tracks, access windows, eclipse | Propagation, Coordinates |

## Dependency Direction

```
Conjunction → Optimization → Propagation → Coordinates
Mission    →                 Propagation → Coordinates
```

No reverse dependencies. Coordinates is the leaf module with zero internal dependencies.

## Module Details

### Coordinates (leaf — implement first)
- Reference frames: ECI (J2000, GCRF, TOD, MOD), ECEF (ITRF), RTN, NTW
- Time systems: UTC, UT1, TAI, TT, GPS, TDB
- Geodetic: WGS84, geodetic ↔ geocentric conversions
- Earth orientation: polar motion, UT1-UTC, precession, nutation
- External data: IERS EOP tables (loaded via MEMFS in WASM)

### Propagation
- Keplerian (analytical, fast, no perturbations)
- SGP4/SDP4 (TLE-based, includes J2 and drag)
- Numerical: RK4, RK7(8) Dormand-Prince, Adams-Bashforth-Moulton
- Perturbations: J2-J6 gravity, atmospheric drag (Harris-Priester, NRLMSISE-00), solar radiation pressure, third-body (Sun, Moon)
- Output: state vectors at requested times, optionally with STM

### Optimization
- Lambert's problem: Izzo universal variable, Gooding method
- Hohmann transfer
- Bi-elliptic transfer
- Low-thrust trajectory optimization (direct collocation)
- Multiple shooting methods
- Delta-V budget calculations

### Conjunction (flagship)
- CDM parser (CCSDS XML format)
- Miss distance computation (radial, in-track, cross-track)
- Collision probability: Foster 1992, Patera 2005, Alfriend 2D/3D
- Monte Carlo risk assessment (configurable samples, parallel execution)
- Screening volumes (box, ellipsoidal)
- Collision avoidance maneuver planning (optimal delta-V)
- Conjunction geometry visualization data (for frontend rendering)

### Mission Analysis
- Ground track generation
- Ground station access windows (elevation mask)
- Line-of-sight analysis
- Eclipse prediction (umbra, penumbra)
- Revisit time calculation
- Orbit maintenance delta-V budget

## External Dependencies

| Library | Purpose | License | WASM Compatible |
| --- | --- | --- | --- |
| Eigen 3.4+ | Linear algebra | MPL2 | Yes |
| Boost (subset) | Utilities, math | BSL-1.0 | Yes (subset) |
| IAU SOFA | Time/coordinate standards | SOFA License | Yes (C version) |
| nlohmann/json | JSON parsing | MIT | Yes |
| Google Test | Testing only | BSD-3 | N/A (not in WASM) |

## Coding Standards

- C++17/20, `-std=c++20` minimum
- All warnings enabled: `-Wall -Wextra -Wpedantic -Werror`
- Smart pointers only (no raw new/delete)
- `constexpr` for compile-time constants
- See `agents/skills/build-pipeline.md` R-012 for naming conventions

## Decision Log

| Date | Decision | Rationale |
| --- | --- | --- |
| 2026-02-14 | Modular library with separate namespaces | Enables independent WASM module loading, reduces bundle size |
| 2026-02-14 | Eigen for linear algebra | Industry standard, header-only, excellent WASM support |
| 2026-02-14 | Coordinates as leaf module | Every other module needs coordinates; it must have zero internal deps |
