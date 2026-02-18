# Testing Skill — Patterns Across All Domains

> Testing rules that apply to every agent and every domain.

## Universal Rules

### T-001: Tests Are Not Optional

Every change that modifies behavior must include tests. "It works on my machine" is not a test. If you can't write a test for it, the requirements aren't clear enough.

### T-002: Test the Contract, Not the Implementation

Tests should verify WHAT a function does, not HOW it does it internally. This means:
- Test inputs and outputs, not internal state
- Don't test private methods directly
- Refactoring should not break tests (if it does, the tests are too coupled)

### T-003: Test Names Are Documentation

Test names should read like specifications:

- GOOD: `propagateOrbit_withJ2Perturbation_matchesSTKWithin1Meter`
- GOOD: `getAccessTier_withExpiredSubscription_returnsFree`
- BAD: `test1`, `testPropagation`, `it_works`

### T-004: AAA Pattern

Every test follows Arrange-Act-Assert:

```cpp
TEST(OrbitPropagator, propagateKeplerian_circularOrbit_returnsToStart) {
  // Arrange
  auto initial_state = createCircularOrbit(EARTH_MU, 6778.0);
  auto propagator = KeplerianPropagator(initial_state);

  // Act
  auto final_state = propagator.propagate(initial_state.period());

  // Assert
  EXPECT_NEAR(final_state.position().norm(),
              initial_state.position().norm(), 1e-6);
}
```

### T-005: Edge Cases to Always Test

For every function, consider:
- Empty/null/zero inputs
- Boundary values (min, max, exactly at threshold)
- Error paths (invalid input, network failure, timeout)
- Concurrent access (if applicable)
- Very large inputs (performance/memory)

For astrodynamics specifically:
- Circular orbits (eccentricity = 0, singularity in some formulations)
- Equatorial orbits (inclination = 0, undefined RAAN)
- Polar orbits (inclination = 90)
- Hyperbolic trajectories (eccentricity > 1)
- Epoch boundary crossings (midnight UTC, leap seconds)
- Degenerate CDMs (zero miss distance, singular covariance)

### T-006: Validation Test Template for Astrodynamics

```cpp
// Reference: [Author, Year, "Paper Title", DOI]
// Source: STK v12.x / GMAT R2022a / OREKIT 12.x
// Test case: [description of reference scenario]
TEST(ModuleName, algorithm_scenario_matchesReference) {
  // Reference values from [source]
  const double expected_position_x = 6778.137; // km
  const double expected_position_y = 0.0;       // km
  const double tolerance = 1.0;                  // meters (see R-004)

  // ... test code ...

  EXPECT_NEAR(result.x(), expected_position_x, tolerance / 1000.0);
}
```

### T-007: Test Categories and When They Run

| Category | Framework | Trigger | Time Budget |
| --- | --- | --- | --- |
| Unit tests | Google Test | Every commit | < 30 seconds |
| Validation tests | Google Test | Every PR | < 5 minutes |
| WASM integration | Playwright | Every PR | < 2 minutes |
| E2E (payment flow) | Jest + Stripe test mode | Pre-release | < 10 minutes |

### T-008: Flaky Test Policy

A flaky test is a bug, not a nuisance.

1. First flake: re-run to confirm. If it passes, add to watch list.
2. Second flake within a week: open a task in `tasks/todo.md`, investigate root cause.
3. Third flake: quarantine the test (skip with explanation), fix within 48 hours.
4. Never delete a flaky test. Fix it or replace it.

Common causes:
- Timing dependencies (use deterministic time in tests)
- Floating point comparison without tolerance
- Test ordering dependencies (each test must stand alone)
- External service calls (mock them)

### T-009: Smart Contract Test Requirements

- Test every public function
- Test access control (who can call what)
- Test fee calculations with multiple scenarios
- Test pause/unpause functionality
- Test with zero balances, max balances, exact thresholds
- Gas usage tests (ensure operations stay within block gas limits)
- Use Hardhat/Foundry test frameworks with forked mainnet state

### T-010: Content Generation "Tests"

Content can't be unit-tested traditionally, but it can be validated:
- Hook length: <= 280 characters for X/Twitter
- Image dimensions: exactly 1024x1536 for TikTok
- Text overlay position: 30% from top
- Hashtag count: <= 5 for TikTok, <= 3 for X
- No broken links in CTAs
- Caption mentions product naturally (not a hard sell)
