---
name: hit-me-plan
description: Use when the user's message contains "hit me" to start an interactive feature/project planning loop, or when continuing an active hit-me-plan loop from an existing root-level plan.MD. The user only needs to say "hit me" once. Continue by capturing each user answer, updating plan.MD, and asking exactly one next question until the user says there are enough questions, enough information, or enough details, then finalize plan.MD into an optimized implementation plan and summarize it.
---

# Hit Me Plan

## Overview

Maintain a root `plan.MD` for the feature or project described by the user. The user says `hit me` once to start; after that, continue the active planning loop from normal user answers until the user says there are enough questions, enough information, or enough details.

## Workflow

1. Resolve the root folder.
   - Prefer the current Git repository root from `git rev-parse --show-toplevel`.
   - If no Git root exists, use the current working directory.
   - Use `plan.MD` exactly as the planning file name.

2. Read any existing `plan.MD`.
   - Treat it as the source of truth for previous planning context.
   - Preserve useful existing content.
   - Repair obvious structure drift directly if the document has become disorganized.
   - If `Current Open Question` is not finalized, treat the conversation as an active hit-me-plan loop even when the latest user message does not contain `hit me`.

3. Capture the latest user input.
   - If the prompt includes a new feature/project description, summarize it under `Overall Idea`.
   - If the prompt answers the previous question, add the answer to `Collected Answers` and fold the implications into the rest of the plan.
   - If the prompt only says `hit me`, create the document with placeholders and ask the first clarifying question.
   - If the prompt says there are enough questions, enough information, enough details, no more questions are needed, "you have enough information", "you have enough details", or a similar stop signal, switch to finalization.
   - Treat obvious typo variants of the stop signal, such as "enought questions" or "enought informations", as finalization requests.

4. Update `plan.MD` before replying.
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

5. If finalizing, reanalyze the full `plan.MD`.
   - Read the entire current document before changing it.
   - Resolve contradictions when the later user answers clearly supersede earlier notes.
   - Reorganize the plan around the best practical implementation path for the feature/project.
   - Break the work into optimal ordered steps if that is not already done.
   - Replace vague intermediate steps with concrete, implementable steps.
   - Add or update `Implementation Plan`, `Acceptance Criteria`, `Risks`, and `Open Decisions` when useful.
   - Set `Current Open Question` to `None - planning finalized.`
   - Do not ask another question in the reply.
   - End the reply with a concise summary of the full plan and implementation steps.

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

Use this shape when creating a new `plan.MD`:

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

After updating `plan.MD`, reply with:

- A brief note that `plan.MD` was created or updated.
- During discovery, exactly one question, matching `## Current Open Question`.
- During finalization, a brief note that `plan.MD` was reorganized into an implementation plan, followed by a concise summary of the full plan and implementation steps, with no question.
