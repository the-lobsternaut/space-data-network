# Code Review Skill — Agent-to-Agent Standards

> Standards for agents reviewing each other's output.
> Following Harness Engineering: agents review agents, humans review when judgment is needed.

## Review Process

### 1. Automated Checks (Must Pass)

Before any agent review, these must pass:
- All tests green
- Lint clean (clang-tidy, clang-format, eslint)
- Bundle size within budget
- No new warnings
- Documentation builds without errors

If automated checks fail, send back immediately. Don't waste review cycles on code that doesn't build.

### 2. Agent Review Checklist

The reviewing agent checks:

**Correctness**
- [ ] Does the change do what the execution plan says it should?
- [ ] Are edge cases handled? (empty inputs, boundary values, error paths)
- [ ] Are error messages helpful? (they should say what went wrong AND what to do about it)

**Architecture**
- [ ] Does the change respect dependency direction rules from ARCHITECTURE.md?
- [ ] Are new modules registered in the right places?
- [ ] No circular dependencies introduced?

**Style and Consistency**
- [ ] Follows naming conventions (see build-pipeline.md R-012)
- [ ] No DRY violations (flag repeated logic > 3 lines)
- [ ] No magic numbers — named constants only
- [ ] Functions do one thing

**Testing**
- [ ] New code has tests
- [ ] Tests cover the happy path AND failure paths
- [ ] Test names describe the scenario
- [ ] Validation tests cite reference sources

**Documentation**
- [ ] Public API changes have updated docs
- [ ] New modules have README.md
- [ ] Design decisions are logged in the execution plan

### 3. Review Verdicts

- **Approve**: All checks pass, no concerns. Merge.
- **Request Changes**: Specific, actionable feedback. List exactly what needs to change.
- **Escalate to Human**: Ambiguous requirements, architectural judgment call, security concern.

### 4. Review Comment Format

```
[SEVERITY] file.cpp:42 — Description

SEVERITY levels:
- BLOCKER: Must fix before merge. Correctness issue or security vulnerability.
- MAJOR: Should fix before merge. Architecture violation or missing tests.
- MINOR: Nice to fix but not blocking. Style preference or minor optimization.
- NOTE: Informational. No action required.
```

## When to Escalate to Human

- Security-sensitive changes (auth, crypto, payment handling)
- Changes that affect user-facing behavior in ambiguous ways
- Architecture decisions not covered by existing design docs
- Disagreement between agents (two agents have conflicting feedback)
- Any change touching private keys, secrets, or credentials
