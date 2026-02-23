# Design Doc: WebAssembly Compilation Pipeline

**Status**: Active
**Owner**: BuildAgent
**Last Updated**: 2026-02-18

## Overview

OrbPro compiles to WebAssembly via Emscripten, enabling browser-native astrodynamics calculations with no server round-trips. The JS/TS API wraps the WASM module for idiomatic usage.

## Build Pipeline

```
C++ Source → CMake → Emscripten (emcc) → WASM + JS glue → npm package
                                        ↓
                                   TypeScript definitions (auto-generated)
```

## Emscripten Configuration

```cmake
# Required flags
-O3                          # Full optimization
-s WASM=1                    # WASM output
-s ALLOW_MEMORY_GROWTH=1     # Dynamic memory allocation
-s MODULARIZE=1              # ES6 module compatible
-s EXPORT_ES6=1              # ES6 export syntax
-s ENVIRONMENT='web,worker'  # Browser + Web Worker targets
-s FILESYSTEM=1              # MEMFS for TLE/CDM file loading
--bind                       # Embind for C++ → JS bindings

# Performance flags
-msimd128                    # SIMD for matrix/vector operations
-pthread                     # Multi-threading via SharedArrayBuffer
-s PTHREAD_POOL_SIZE=4       # 4 worker threads

# Debug flags (dev builds only)
-g                           # Debug symbols
-s ASSERTIONS=2              # Runtime assertions
-s SAFE_HEAP=1               # Memory access checks
```

## Module Splitting Strategy

Split WASM into independent modules to reduce initial load:

| Module | Contains | Estimated Size |
| --- | --- | --- |
| `orbpro-core.wasm` | Coordinates + Propagation | ~300 KB gzipped |
| `orbpro-conjunction.wasm` | Conjunction analysis | ~150 KB gzipped |
| `orbpro-optimization.wasm` | Lambert, transfers | ~100 KB gzipped |
| `orbpro-mission.wasm` | Mission analysis | ~100 KB gzipped |

Users load only what they need. Core module is required; others are optional.

## JavaScript API Design

Idiomatic TypeScript wrapper abstracting WASM complexity:

```typescript
// @lobsternaut/orbpro
import { Propagator, ConjunctionAnalyzer, type AccessTier } from '@lobsternaut/orbpro';

// Initialize (loads WASM, one-time)
await OrbPro.initialize();

// Propagation
const sat = new Propagator(tleString);
const states = sat.propagate(startTime, endTime, stepSizeSeconds);

// Conjunction Assessment
const analyzer = new ConjunctionAnalyzer(cdmXmlString);
const result = analyzer.calculateCollisionProbability('foster1992');
console.log(`Pc = ${result.probability}, TCA = ${result.tca}`);

// Access control integrated
const analyzer = new ConjunctionAnalyzer(cdmXml, { accessToken });
// Automatically limits methods based on tier
```

## Embind Binding Strategy

Every public C++ class/function gets an Embind declaration:

```cpp
#include <emscripten/bind.h>

EMSCRIPTEN_BINDINGS(propagation) {
  class_<OrbitPropagator>("Propagator")
    .constructor<std::string>()  // TLE string
    .function("propagate", &OrbitPropagator::propagate)
    .function("getState", &OrbitPropagator::getState);
}
```

TypeScript definitions auto-generated from these bindings.

## npm Package Structure

```
@lobsternaut/orbpro/
├── dist/
│   ├── orbpro-core.wasm
│   ├── orbpro-core.js          # JS glue code
│   ├── orbpro-conjunction.wasm
│   ├── orbpro-conjunction.js
│   ├── index.js                # ES6 entry point
│   ├── index.cjs               # CommonJS entry point
│   └── index.d.ts              # TypeScript definitions
├── package.json
└── README.md
```

## Testing in Browser Environment

WASM tests run via Playwright in a headless browser:

1. Start a local dev server serving the WASM files
2. Playwright loads a test page that imports the JS API
3. Test page runs calculations and reports results
4. Compare results against C++ unit test expected values
5. Verify: same inputs → same outputs in both C++ and WASM

## Decision Log

| Date | Decision | Rationale |
| --- | --- | --- |
| 2026-02-14 | Embind over WebIDL | Better TypeScript integration, more intuitive API |
| 2026-02-14 | Module splitting | Reduce initial load time, users load what they need |
| 2026-02-14 | MEMFS for file loading | Load TLE/CDM files in-browser without server |
| 2026-02-14 | SharedArrayBuffer for threading | Parallel orbit propagation, Monte Carlo |
