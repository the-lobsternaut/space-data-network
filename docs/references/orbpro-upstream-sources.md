# OrbPro Upstream Source Links

**Status**: Active  
**Owner**: BuildAgent  
**Last Updated**: 2026-02-23

## Purpose

OrbPro is maintained as part of the Lobsternaut product codebase, not as a separate standalone open-source library project.

This file tracks upstream open-source astrodynamics and supporting code used to build, validate, or benchmark OrbPro, so implementation provenance stays transparent.

## Runtime and Build Dependencies

| Project | Usage in OrbPro | Link | License |
| --- | --- | --- | --- |
| IAU SOFA (C) | Time standards and reference frame transforms | https://www.iausofa.org/ | SOFA |
| Eigen | Linear algebra and matrix math | https://gitlab.com/libeigen/eigen | MPL-2.0 |
| Boost (subset) | Utility and math components | https://www.boost.org/ | BSL-1.0 |
| nlohmann/json | JSON parsing and serialization | https://github.com/nlohmann/json | MIT |
| Google Test | C++ unit testing | https://github.com/google/googletest | BSD-3-Clause |

## Algorithm and Validation References (Open Source)

| Project | Usage in OrbPro | Link | License |
| --- | --- | --- | --- |
| Orekit | Cross-validation of orbital mechanics implementations and expected behavior | https://github.com/CS-SI/Orekit | Apache-2.0 |
| Python SGP4 | Additional SGP4 behavior checks in test harnesses | https://github.com/brandon-rhodes/python-sgp4 | MIT |

## Maintenance Rules

1. Add every new upstream open-source code source before merging implementation changes that depend on it.
2. Record license and intended usage scope for each source.
3. Remove sources that are no longer used and document the removal reason in `tasks/lessons.md`.
4. Keep this list aligned with `docs/design-docs/orbpro-architecture.md` dependency declarations.
