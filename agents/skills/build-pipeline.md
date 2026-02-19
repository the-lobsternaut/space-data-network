# Build Pipeline Skill — Rules and Patterns

> Rules for managing the OrbPro C++ library, WASM compilation, and CI/CD.
> ~200 lines initial seed. Grows as build failures and optimizations are discovered.

## Core Rules

### R-001: Module Scaffold Is Sacred

Every OrbPro module follows this exact layout. No exceptions.

```
src/<module>/
├── include/<module>/    # Public headers (.h) — only these are installed
├── src/                 # Implementation (.cpp)
├── test/                # Google Test files
└── README.md            # Module documentation
```

New modules must be registered in:
1. Top-level `CMakeLists.txt`
2. `ARCHITECTURE.md` module table
3. `docs/design-docs/orbpro-architecture.md`

### R-002: No Raw Memory Management

- Always use smart pointers (`std::unique_ptr`, `std::shared_ptr`)
- No raw `new` / `delete`
- Use `std::vector` and `std::array`, not C-style arrays
- Exception: performance-critical inner loops where profiling proves smart pointers are the bottleneck (document with profiling evidence)

### R-003: Every Algorithm Needs a Reference

No numerical algorithm is implemented without a cited reference:
- Published paper (author, year, DOI or title)
- Textbook (author, edition, page)
- Validated software (STK, GMAT, OREKIT with specific test case)

The reference goes in:
1. The source file header comment
2. The test file (as a comment explaining the expected values)
3. `docs/design-docs/orbpro-architecture.md` references section

### R-004: Validation Tolerances

These tolerances are non-negotiable. If a test exceeds them, the algorithm has a bug.

| Quantity | Tolerance | Reference Period |
| --- | --- | --- |
| LEO position | < 1 meter | 24-hour propagation |
| LEO velocity | < 1 mm/s | 24-hour propagation |
| GEO position | < 10 meters | 7-day propagation |
| Collision probability | < 1% relative error | Against Foster 1992 cases |
| Time conversion | < 1 microsecond | UTC/TAI/TT/GPS round-trip |
| Coordinate transform | < 1 milliarcsecond | ECI/ECEF round-trip |

### R-005: WASM Build Flags

Standard Emscripten build configuration:

```cmake
set(WASM_FLAGS
  -O3                          # Full optimization
  -s WASM=1                    # Output WASM (not asm.js)
  -s ALLOW_MEMORY_GROWTH=1     # Dynamic memory
  -s MODULARIZE=1              # ES6 module output
  -s EXPORT_ES6=1              # ES6 export syntax
  -s ENVIRONMENT='web,worker'  # Browser + Web Worker
  -s FILESYSTEM=1              # MEMFS for file loading
  --bind                       # Embind for C++ bindings
)

# Optional performance flags
set(WASM_PERF_FLAGS
  -msimd128                    # SIMD for vector operations
  -pthread                     # Threading via SharedArrayBuffer
  -s PTHREAD_POOL_SIZE=4       # Worker thread pool
)
```

### R-006: Bundle Size Budget

| Target | Limit | Action if Exceeded |
| --- | --- | --- |
| Core WASM (gzipped) | 500 KB | Required — investigate what's bloating |
| Full WASM with all modules (gzipped) | 2 MB | Required — consider code splitting |
| npm package total | 5 MB | Warning — review included assets |

CI fails if core WASM exceeds 500 KB gzipped. Measure with: `gzip -c orbpro.wasm | wc -c`

### R-007: TypeScript Definitions Are Auto-Generated

TypeScript `.d.ts` files are generated from Embind bindings, never hand-written. Process:

1. Embind declarations in C++ define the JS API surface
2. Build step generates `.d.ts` from Embind metadata
3. Generated files go in `wasm/types/` (treated as generated, not hand-edited)
4. npm package includes generated types

If the types are wrong, fix the Embind declarations, not the generated files.

### R-008: Test Categories

Three categories of tests, all required:

**Unit Tests** (Google Test, fast, run on every commit)
- Test individual functions in isolation
- Mock external dependencies
- Target: < 30 seconds total runtime

**Validation Tests** (Google Test, slower, run on PR)
- Compare against reference data from STK/GMAT/OREKIT
- Use the tolerances from R-004
- Target: < 5 minutes total runtime

**WASM Integration Tests** (Playwright, browser, run on PR)
- Load the WASM module in a headless browser
- Call the JavaScript API
- Verify results match C++ unit test expectations
- Target: < 2 minutes total runtime

### R-009: CI Pipeline Structure

```yaml
# GitHub Actions — required checks
jobs:
  build-cpp:          # CMake build on Linux, macOS, Windows
  test-unit:          # Google Test — unit tests
  test-validation:    # Google Test — reference data validation
  build-wasm:         # Emscripten WASM compilation
  test-wasm:          # Playwright browser tests
  lint:               # clang-tidy + clang-format
  bundle-size:        # Measure and gate WASM size
  docs:               # Doxygen generates without warnings

# Optional checks
  benchmark:          # Performance comparison against baseline
  sanitizers:         # AddressSanitizer, UBSanitizer
```

### R-010: Build Failure Classification

When a build fails, classify and respond:

| Classification | Response |
| --- | --- |
| Compile error | Fix source code, never suppress with pragmas |
| Link error | Check CMakeLists.txt, verify module dependencies |
| Test failure (deterministic) | This is a regression — fix the code, not the test |
| Test failure (flaky) | Add to known flakes, investigate root cause within 48 hours |
| Timeout | Check for O(n^2) or worse, add complexity bounds |
| Bundle size exceeded | Profile what's included, tree-shake or split modules |
| Lint failure | Fix formatting, never disable the lint rule |

### R-011: Dependency Management

**Allowed dependencies** (well-documented, stable, good LLM training coverage):
- Eigen (linear algebra)
- Boost (utilities — use only what's needed, not the whole library)
- IAU SOFA (time and coordinate standards)
- nlohmann/json (JSON parsing)
- Google Test (testing)

**Adding a new dependency requires**:
1. Justification in a design doc
2. License compatibility check (must be permissive: MIT, BSD, Apache 2.0)
3. Assessment of Emscripten compatibility
4. Size impact measurement on WASM bundle

### R-012: Naming Conventions

| Element | Convention | Example |
| --- | --- | --- |
| Class | PascalCase | `OrbitPropagator` |
| Function | camelCase | `propagateOrbit()` |
| Variable | camelCase | `timeStep` |
| Constant | UPPER_SNAKE | `EARTH_MU` |
| File (header) | PascalCase.h | `OrbitPropagator.h` |
| File (source) | PascalCase.cpp | `OrbitPropagator.cpp` |
| File (test) | PascalCase_test.cpp | `OrbitPropagator_test.cpp` |
| Namespace | lowercase | `orbpro::propagation` |
| CMake target | lowercase-hyphen | `orbpro-propagation` |

## Definition of Done — Build Tasks

A build task is complete when ALL of the following are verified:

- [ ] C++ code compiles without errors on all target platforms
- [ ] All unit tests pass (< 30 second runtime)
- [ ] All validation tests pass within R-004 tolerances
- [ ] WASM build succeeds via Emscripten
- [ ] WASM integration tests pass in headless browser
- [ ] Bundle size is within budget (R-006): core < 500 KB gzipped
- [ ] TypeScript definitions are auto-generated and current (R-007)
- [ ] Lint is clean (clang-tidy, clang-format)
- [ ] No new warnings introduced
- [ ] If API surface changed: DocumentationAgent notified via handoff suggestion
- [ ] Handoff produced per `agents/skills/shared/handoff-protocol.md`

For CI/CD changes specifically:
- [ ] Pipeline runs end-to-end on a test push
- [ ] All required checks pass
- [ ] Build time is under 10 minutes

## Failure Log

> Build failures and their resolutions.

_No failures logged yet. Begin logging as builds are set up._

## Optimization Notes

> WASM-specific optimizations discovered through benchmarking.

_No optimizations logged yet. Begin after first WASM benchmarks._
