# Product Spec: Conjunction Assessment (Flagship Feature)

**Status**: Active
**Owner**: BuildAgent
**Last Updated**: 2026-02-18

## Overview

Conjunction assessment is OpenClaw's flagship feature — analyzing the risk of two objects colliding in orbit. This is critical for the growing space debris problem affecting satellite operators globally.

The baseline screening workflow is free and open-source as OpenClaw's shared space-safety utility.

## User Stories

1. **Satellite Operator**: "I need to know if my satellite is at risk of collision in the next 7 days and what maneuver to perform to avoid it."
2. **Aerospace Student**: "I want to understand how collision probability is calculated and see it visualized."
3. **Space Enthusiast**: "I want to see which objects in orbit are closest to colliding right now."

## Feature Tiers

| Capability | Free | Bronze | Silver | Gold |
| --- | --- | --- | --- | --- |
| Basic screening (miss distance + geometry) | Yes | Yes | Yes | Yes |
| Full CDM parsing and analysis | No | Yes | Yes | Yes |
| Monte Carlo risk assessment | No | No | Yes | Yes |
| Collision avoidance maneuver planning | No | No | No | Yes |
| Conjunction geometry visualization | Yes | Yes | Yes | Yes |
| Historical conjunction database | No | No | Yes | Yes |
| Real-time alerts | No | No | No | Yes |

## Technical Requirements

### Input Formats
- CCSDS Conjunction Data Messages (CDM) — XML format
- Two-Line Element sets (TLE) for independent screening
- State vectors (position + velocity in ECI J2000)
- Covariance matrices (6x6 in RTN or ECI frame)

### Collision Probability Methods
1. **Foster 1992** — 2D projection method (fastest, good for screening)
2. **Patera 2005** — Line-of-sight integration (more accurate for high Pc)
3. **Alfriend 2D** — Analytic 2D method
4. **Alfriend 3D** — Full 3D numerical integration (most accurate, slowest)
5. **Monte Carlo** — Statistical sampling (Silver tier+, configurable samples)

### Output
- Probability of collision (Pc) with confidence interval
- Time of closest approach (TCA)
- Miss distance (radial, in-track, cross-track components)
- Combined covariance ellipse visualization
- Maneuver recommendation (Gold tier): delta-V magnitude, direction, timing

### Performance Requirements
- Foster 1992 Pc calculation: < 10ms
- Patera 2005 Pc calculation: < 100ms
- Monte Carlo (10,000 samples): < 5 seconds
- Full maneuver optimization: < 30 seconds
- WASM module load time: < 2 seconds

## Validation

All methods validated against:
- STK CARA (Conjunction Assessment Risk Analysis) module
- NASA CARA operational results (published test cases)
- ESA CREAM (Collision Risk Estimation and Automated Mitigation)
- Published academic test cases (Foster 1992 paper Table 1)
