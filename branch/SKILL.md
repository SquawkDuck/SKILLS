---
name: branch
description: Use when the user says "dry it" or asks for a current feature-branch review focused on drying up code. Inspect all changes from the beginning of the current feature branch, including uncommitted work, and review or edit for stale leftovers, dead code, duplication, repeated components that should use shared components, and opportunities to shorten code without compromising behavior, stability, or tests.
---

# Branch

## Overview

Use this skill to review the whole current feature branch, not just the latest
diff. The default outcome is practical cleanup: remove stale code, reduce
duplication, reuse existing shared components or helpers, and keep behavior and
tests intact. Push hard to make the branch shorter, but only when the smaller
version is as stable, readable, and well-covered as the original.

## Workflow

1. Establish the branch scope.
   - Confirm the repository and current state with `git status --short` and
     `git branch --show-current`.
   - Find the branch base. Prefer `git merge-base HEAD @{upstream}` when an
     upstream exists; otherwise try `origin/main`, `origin/master`, `main`, then
     `master`.
   - Include committed, staged, and unstaged changes. Compare the committed
     branch with `git diff <base>...HEAD`, then inspect `git diff --cached` and
     `git diff`.
   - Read applicable repo instructions before edits, including root and
     subtree-specific `AGENTS.md` files when present.

2. Build a change map.
   - List changed files and scale with `git diff --stat <base>...HEAD`,
     `git diff --name-status <base>...HEAD`, `git diff --cached --name-status`,
     and `git diff --name-status`.
   - Read the branch diff plus the current versions of changed files.
   - Read nearby shared components, hooks, helpers, constants, tests, and
     patterns before deciding something is duplicated.
   - Search with `rg` for distinctive names, copied labels, repeated JSX,
     validators, mappers, selectors, styles, API calls, constants, test setup,
     and old feature names.

3. Hunt for branch leftovers.
   - Look for commented-out implementations, unused imports or exports, orphaned
     props/state, stale feature flags, unused helpers, abandoned TODOs, old test
     fixtures, obsolete copy, duplicated branches, and files created by an
     earlier version of the feature.
   - Check whether new code still carries fallback paths for data shapes,
     components, routes, or APIs that the branch no longer uses.
   - Treat generated files, lockfiles, migrations, snapshots, and vendored code
     cautiously; verify ownership before shortening them.

4. Shorten and reuse carefully.
   - Delete code that is clearly unused, redundant, or superseded by current
     branch behavior.
   - Eliminate extra code that does not carry behavior, compatibility,
     observability, or test value.
   - Reuse an existing shared component, hook, helper, constant, or test utility
     when it matches the semantics and keeps the call site clearer.
   - Extract a new shared piece only when duplication is meaningful, local
     patterns support it, and the abstraction is smaller than the repeated code.
   - Collapse redundant conditionals, pass-through wrappers, repeated mappings,
     and duplicated UI fragments without changing public contracts.
   - Preserve user-visible copy, accessibility, analytics, API payloads,
     loading/error states, permissions, and edge-case handling unless the diff
     proves they are obsolete.
   - Do not remove tests that protect live behavior. Replace noisy tests only
     with stronger or equivalent coverage.

5. Verify the cleanup.
   - Run the most relevant existing checks for the touched surface: unit tests,
     type checks, lint, build, or focused browser verification for UI behavior.
   - If a full check is too expensive or blocked, run the narrowest defensible
     check and state the remaining risk.
   - Review the final diff with `git diff --stat` and `git diff` before
     finishing. Confirm the result is shorter or more reusable for the right
     reasons, not just rearranged.

## Output

- Lead with findings when the user asked for review only.
- When edits were made, summarize the cleanup, files changed, and verification.
- Always mention the branch base used, the checks run, and any residual risk.
- Call out any duplication or leftover code intentionally left in place and why.
