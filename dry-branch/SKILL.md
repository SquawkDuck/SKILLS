---
name: dry-branch
description: Use when the user says "dry the branch" or asks for a current feature-branch review focused on drying up code. Inspect the whole branch diff first, scan commit history for suspicious rewrites or leftovers, include uncommitted work, and review or edit for stale code, duplication, repeated components that should use shared components, and opportunities to shorten code without compromising behavior, stability, or tests.
---

# Dry Branch

## Overview

Use this skill to review the whole current feature branch before looking at
individual commits. The default outcome is practical cleanup: remove stale code,
reduce duplication, reuse existing shared components or helpers, and keep
behavior and tests intact. Push hard to make the branch shorter, but only when
the smaller version is as stable, readable, and well-covered as the original.

## Workflow

1. Establish the branch scope.
   - Confirm the repository and current state with `git status --short` and
     `git branch --show-current`.
   - Find the branch base. Prefer `git merge-base HEAD @{upstream}` when an
     upstream exists; otherwise try `origin/main`, `origin/master`, `main`, then
     `master`.
   - Read applicable repo instructions before edits, including root and
     subtree-specific `AGENTS.md` files when present.

2. Review the whole branch diff first.
   - Compare the committed branch with `git diff <base>...HEAD`.
   - Include staged and unstaged changes with `git diff --cached` and
     `git diff`.
   - Inspect the current versions of changed files as they stand now, not only
     the patch text.
   - List changed files and scale with `git diff --stat <base>...HEAD`,
     `git diff --name-status <base>...HEAD`, `git diff --cached --name-status`,
     and `git diff --name-status`.
   - Read nearby shared components, hooks, helpers, constants, tests, and
     patterns before deciding something is duplicated.
   - Search with `rg` for distinctive names, copied labels, repeated JSX,
     validators, mappers, selectors, styles, API calls, constants, test setup,
     and old feature names.

3. Scan commit history.
   - Run `git log --reverse --name-status <base>..HEAD`.
   - Look for big rewrites, renamed files, deleted files, WIP commits, repeated
     areas, old component names, and reverted approaches.
   - Use the history scan to guide cleanup, not to review every commit with the
     same depth.

4. Deep-dive only suspicious commits.
   - Inspect commits that introduced code later replaced by newer branch work.
   - Inspect commits touching the same component, helper, hook, API, or test
     utility repeatedly.
   - Inspect commits that add duplicate UI, duplicate logic, or near-copy test
     setup.
   - Inspect commits with leftovers such as old props, unused helpers, stale
     tests, abandoned files, obsolete copy, or reverted approach names.

5. Hunt for branch leftovers in the current tree.
   - Look for commented-out implementations, unused imports or exports, orphaned
     props/state, stale feature flags, unused helpers, abandoned TODOs, old test
     fixtures, obsolete copy, duplicated branches, and files created by an
     earlier version of the feature.
   - Check whether new code still carries fallback paths for data shapes,
     components, routes, or APIs that the branch no longer uses.
   - Treat generated files, lockfiles, migrations, snapshots, and vendored code
     cautiously; verify ownership before shortening them.

6. Shorten and reuse carefully.
   - Use commit history to find stale intent, but apply fixes only to the
     current working tree.
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
   - Keep tests and behavior stable. Do not remove tests that protect live
     behavior; replace noisy tests only with stronger or equivalent coverage.

7. Verify the cleanup.
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
