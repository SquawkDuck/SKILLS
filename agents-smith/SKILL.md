---
name: agents-smith
description: Use when the user explicitly asks for Agents Smith, multi-agent execution, multiple agents, delegated agents, parallel agents, or when a finalized plan hands off to this skill. Split non-trivial work by concern, assign bounded agent roles and file ownership, run useful sidecar work in parallel, integrate results, and use bundled Dariobase layered-agent fallback rules when local AGENTS.md guides are missing.
---

# Agents Smith

## Overview

Use this skill to turn a non-trivial task or finalized plan into a practical
multi-agent execution strategy. Keep the split small, concrete, and tied to the
actual concerns in the work.

This skill is independent from planning skills. A planning skill may hand off to
`$agents-smith`, but this skill owns only the execution split, delegation, and
integration discipline.

For Dariobase work, this skill is self-contained: if the current branch does
not contain the layered `AGENTS.md` files, use the bundled fallback guide in
`references/dariobase-layered-agents.md`.

## When To Use

Use Agents Smith when one of these is true:

- The user explicitly asks for `Agents Smith`, multi-agent execution, multiple
  agents, delegated agents, or parallel agents.
- A finalized plan or implementation handoff explicitly invokes
  `$agents-smith`.
- The task is broad enough that independent concerns can be explored or changed
  in parallel, and the user has asked for a multi-agent approach.

Do not force multi-agent work for small, direct, single-file tasks. Keep those
local unless the user explicitly asks otherwise.

## Workflow

1. Read the task or finalized plan and identify the immediate critical path.
   - Decide what must be done locally first.
   - Do not delegate work that blocks the very next local step.
   - Continue local work while agents handle independent sidecar tasks.

2. Split by concern, not by arbitrary file count.
   - Useful concerns include frontend UI, backend endpoints, shared models,
     data migration, tests, live browser verification, git history, and docs.
   - Keep each delegated task concrete and self-contained.
   - Avoid assigning two agents to the same unresolved question.

3. Load the right repo rules for each concern.
   - Prefer current local `AGENTS.md` files when they exist.
   - For Dariobase branches without those files, read
     `references/dariobase-layered-agents.md` and apply the matching section.
   - Include the relevant local or bundled rules in each agent brief.

4. Choose agent roles.
   - Use explorers for bounded read-only codebase questions.
   - Use workers for bounded implementation or test changes.
   - Prefer workers only when the write scope is clear and can be kept
     disjoint from other workers.

5. Give each agent a narrow brief.
   - State the exact question or implementation outcome.
   - State which repo guide or bundled fallback section applies.
   - For coding work, state file or module ownership.
   - Tell workers they are not alone in the codebase, must not revert other
     edits, and should adjust to parallel changes.
   - Ask coding workers to edit files directly and list changed paths.

6. Integrate results locally.
   - Review returned findings or patches before relying on them.
   - Resolve conflicts and keep final ownership with the main agent.
   - Run focused verification for the integrated behavior.

7. Report the execution split.
   - Mention which agents were used and what each contributed.
   - If no subagents were spawned, say why, for example the task stayed
     single-surface or blocked on one critical path.

## Delegation Patterns

Use parallel explorers when separate questions can be answered independently:

- One explorer traces the UI path.
- One explorer traces the endpoint/model path.
- One explorer checks tests, history, or existing regressions.

Use parallel workers only when write ownership is disjoint:

- One worker owns frontend files.
- One worker owns backend/model files.
- One worker owns tests or docs.

Keep verification parallel only when it can run without blocking active local
implementation.

## Prompt Shape

For an explorer:

```text
Investigate [specific concern]. Apply [local AGENTS.md or bundled fallback
section]. Return exact files, functions, and the smallest evidence needed to
answer the question. Do not edit files.
```

For a worker:

```text
Implement [specific outcome] in [owned files/modules]. Apply [local AGENTS.md
or bundled fallback section]. You are not alone in the codebase; do not revert
other edits. Keep the write scope narrow, adapt to parallel changes, and list
changed paths in your final answer.
```

## Bundled References

- `references/dariobase-layered-agents.md` contains the Dariobase root and
  scoped guide content from the layered agent system. Read it when working in
  Dariobase on a branch that lacks those `AGENTS.md` files, or when a spawned
  agent needs the relevant rules embedded in its brief.

## Guardrails

- Do not spawn agents unless the current user request or handoff explicitly
  asks for multi-agent, delegated, or parallel work.
- Do not wait for agents by default. Wait only when their result blocks the next
  critical-path step.
- Do not redo delegated work locally while the agent is running.
- Do not let agents make overlapping edits unless the overlap is unavoidable
  and explicitly coordinated.
- Prefer fewer, better-scoped agents over a large pool.
