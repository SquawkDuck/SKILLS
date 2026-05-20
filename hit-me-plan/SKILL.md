---
name: hit-me-plan
description: Use when the user's message contains "hit me" to start an interactive feature/project planning loop, or when continuing an active hit-me-plan loop from an existing feature-root plan.MD. The user only needs to say "hit me" once. Continue by capturing each user answer, updating the feature's plan.MD, and asking exactly one next question until the user says there are enough questions, enough information, or enough details, then finalize plan.MD into an optimized implementation plan, summarize it, and hand off by entering Plan mode.
---

# Hit Me Plan

## Overview

Maintain one `plan.MD` per feature or project in that feature's root folder. The
user says `hit me` once to start; after that, continue the active planning loop
from normal user answers until the user says there are enough questions, enough
information, or enough details.

## Workflow

1. Resolve the feature root folder.
   - Save the planning file in the root folder of the specific feature or
     project being planned, not in the Git repository root by default.
   - Prefer an explicit feature/module path from the user when one is provided.
   - If the user gives file paths, resolve the smallest stable directory that
     owns the feature rather than the deepest leaf file directory.
   - If no path is provided, inspect the repository enough to infer the most
     likely feature root from existing modules, routes, pages, or components.
   - If the feature root cannot be inferred safely, ask exactly one question
     asking where the feature should live before creating a planning file.
   - Use `plan.MD` exactly as the planning file name inside the resolved feature
     root.
   - Never create or update a planning file in the repository root unless the
     feature itself is genuinely rooted at the repository root.

2. Read any existing `plan.MD` in the resolved feature root.
   - Treat it as the source of truth only when it describes the same feature or
     project.
   - Preserve useful existing content.
   - Repair obvious structure drift directly if the document has become disorganized.
   - If `Current Open Question` is not finalized and the plan describes the
     same feature, treat the conversation as an active hit-me-plan loop even
     when the latest user message does not contain `hit me`.
   - If an existing `plan.MD` in that folder describes a different feature, do
     not overwrite it. Resolve a more specific feature root or ask one question
     for the correct feature location.

3. Capture the latest user input.
   - If the prompt includes a new feature/project description, summarize it under `Overall Idea`.
   - If the prompt answers the previous question, add the answer to `Collected Answers` and fold the implications into the rest of the plan.
   - If the prompt only says `hit me`, create the document with placeholders in
     the resolved feature root and ask the first clarifying question.
   - If the prompt says there are enough questions, enough information, enough details, no more questions are needed, "you have enough information", "you have enough details", or a similar stop signal, switch to finalization.
   - Treat obvious typo variants of the stop signal, such as "enought questions" or "enought informations", as finalization requests.

4. Update the resolved feature-root `plan.MD` before replying.
   - Keep the document concise and useful, not a transcript.
   - Include at least these sections:
     - `# Plan`
     - `## Overall Idea`
     - `## Known Details`
     - `## Collected Answers`
     - `## Intermediate Steps`
     - `## Current Open Question`
   - Add other sections only when the user's feature/project needs them, such as `Constraints`, `Risks`, or `Acceptance Criteria`.
   - Keep `Current Open Question` in sync with the one question asked in the reply.

5. If finalizing, reanalyze the full resolved feature-root `plan.MD`.
   - Read the entire current document before changing it.
   - Resolve contradictions when the later user answers clearly supersede earlier notes.
   - Reorganize the plan around the best practical implementation path for the feature/project.
   - Break the work into optimal ordered steps if that is not already done.
   - Replace vague intermediate steps with concrete, implementable steps.
   - Add or update `Implementation Plan`, `Acceptance Criteria`, `Risks`, and `Open Decisions` when useful.
   - Decide whether implementation needs multiple agents. If the plan can be
     handled cleanly by a single agent on one critical path, do not call
     `$agents-smith` and do not add a multi-agent handoff.
   - Only if implementation crosses multiple independent concerns, add an
     `Execution Handoff` section at the end of the plan calling
     `$agents-smith` for the next execution step. Keep the detailed
     multi-agent split out of this skill.
   - Set `Current Open Question` to `None - planning finalized.`
   - Do not ask another question in the reply.
   - End the reply with a concise summary of the full plan and implementation steps.
   - Treat entering Plan mode as the final handoff after finalization.
   - If the runtime can switch modes directly, enter Plan mode after the final
     summary.
   - If the runtime cannot switch modes directly, finish the reply with a clear
     instruction for the user to enter Plan mode before implementation.

6. If not finalizing, ask exactly one question.
   - Ask the single most useful next question for refining the feature/project.
   - Do not ask a list of questions.
   - Do not combine multiple questions with "and/or".
   - Prefer questions about scope, user workflow, acceptance criteria, data, UI behavior, or constraints.
   - Phrase the question so the user can answer naturally without repeating `hit me`.

## Document Style

- Use plain Markdown.
- Use short bullets for details and numbered lists for step order.
- Write specific project details over generic planning advice.
- Mark unknowns as `TBD` only when a question is needed to fill them.
- When finalizing, remove unnecessary `TBD` placeholders or move unresolved items to `Open Decisions`.
- Do not implement code or scene changes while using this skill unless the user explicitly asks to leave planning mode.

## First-Turn Template

Use this shape when creating a new feature-root `plan.MD`:

```markdown
# Plan

## Overall Idea

TBD

## Known Details

- TBD

## Collected Answers

- TBD

## Intermediate Steps

1. Define the feature/project goal.
2. Clarify the target user workflow.
3. Identify constraints and acceptance criteria.
4. Break implementation into small ordered steps.

## Current Open Question

TBD
```

Replace `TBD` entries immediately when the user provides real information.

## Reply Format

After updating the feature-root `plan.MD`, reply with:

- A brief note that `plan.MD` was created or updated, including its feature
  root path when that helps disambiguate multiple plans.
- During discovery, exactly one question, matching `## Current Open Question`.
- During finalization, a brief note that `plan.MD` was reorganized into an
  implementation plan, followed by a concise summary of the full plan and
  implementation steps. Include the `$agents-smith` handoff only when the plan
  crosses independent concerns; otherwise omit it. Then provide the Plan mode
  handoff, with no question.
