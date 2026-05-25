---
name: regression-tests
description: Use when the user says "sling the branch", asks to add final tests after feature development, or wants regression-test coverage for the current branch. Inspect branch changes, identify changed behavior and regression risks, add or update focused automated tests where practical, run relevant checks, and report any remaining untested gaps.
---

# Regression Tests

## Overview

Add final regression coverage after feature work is implemented. Treat this as
an execution workflow: inspect the branch, choose focused checks that protect
old behavior and the new behavior, add or update tests, run them, and report the
result.

## Workflow

1. Ground in the branch.
   - Read repo and scoped instruction files before editing test code.
   - Inspect git status, branch diff, touched files, and recent commits enough
     to understand the implemented behavior.
   - Preserve unrelated user changes. Do not revert or rewrite production code
     unless the test work exposes a real bug that must be fixed to make the
     requested behavior pass.

2. Find the existing test shape.
   - Search for tests near the changed modules, shared helpers, routes,
     components, API handlers, or data contracts.
   - Prefer the repository's existing test framework, fixtures, factories,
     naming, and command wrappers.
   - Discover likely test commands from package scripts, build files, repo
     instructions, and nearby test documentation instead of inventing commands.

3. Decide the regression coverage.
   - Cover the behavior that changed and the old behavior most likely to break.
   - Include at least one focused regression assertion for the original risk or
     bug class when that risk can be inferred.
   - Add edge cases only when they protect a realistic failure mode introduced
     by the branch.
   - Prefer automated tests. Use manual verification only for browser, visual,
     integration, permission, timing, or third-party behavior that is not
     practical to automate in the current repo.

4. Add or update tests.
   - Keep test edits close to the affected ownership boundary.
   - Update existing tests when that better matches local patterns; add new
     tests when the behavior has no suitable nearby coverage.
   - Avoid broad snapshots, brittle selectors, or oversized fixtures unless the
     repo already uses them for the same kind of behavior.
   - Keep production changes out of scope except for minimal testability fixes
     or bug fixes discovered by the tests.

5. Run verification.
   - Run the narrowest relevant test command first.
   - Run broader related tests when the change touches shared logic,
     cross-module contracts, permissions, migrations, or user-facing workflows.
   - If a command cannot run because of environment limits or missing services,
     capture the exact blocker and provide the command that should be run later.
   - If failures look unrelated to the branch, preserve the output summary and
     continue with the most useful remaining checks.

6. Report clearly.
   - Summarize changed or added tests.
   - List commands run and their outcomes.
   - Call out manual checks performed or still needed.
   - State any important regression gap that remains untested and why.

## Coverage Heuristics

- UI changes: cover render state, user interaction, disabled/error/loading
  states, and the previous visual or data-path regression when testable.
- API or backend changes: cover success, validation/error, authorization or
  tenancy boundaries, and serialization/data-contract compatibility.
- Data/model changes: cover migration or conversion behavior, missing/legacy
  values, and callers that rely on the previous shape.
- Shared utility changes: cover representative callers, boundary inputs, and
  backward-compatible behavior.
- Bug fixes: include a test that would have failed before the fix and passes
  after it.
