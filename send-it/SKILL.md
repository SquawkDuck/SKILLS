---
name: send-it
description: Build and deploy web-deployable projects to Vercel preview deployments. Use when the user says "send it", asks to build and deploy, asks for a Vercel preview deployment, or wants a game, website, static app, frontend app, Unity WebGL build, or other web build shipped to Vercel.
---

# Send It

## Overview

Build the current project using its existing setup, perform first-time Vercel setup when needed, and deploy a Vercel preview by default.

## Workflow

1. Inspect before acting.
   - Read local instructions first, such as `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or repo-specific docs.
   - Check for Vercel setup in `.vercel/project.json`, `vercel.json`, and package scripts.
   - Identify the project type and build path from existing files: `package.json`, framework configs, static `index.html`, Unity project markers, existing build output, CI files, or documented commands.
   - If Vercel-specific skills or tools are available, use their CLI/deployment guidance for current command behavior.

2. Confirm the deployment target.
   - Default to a Vercel preview deployment.
   - Do not deploy production with `--prod` unless the user explicitly asks for production.
   - If the repo is not linked to Vercel, run `vercel link`; prefer explicit `--project` and `--scope` values when they are known.
   - If linking requires a team or project choice that cannot be inferred safely, ask the user and present the concrete options shown by the CLI or repository context.

3. Build with the existing setup.
   - Prefer existing build scripts, framework defaults, or Vercel configuration over creating new build plumbing.
   - Use `vercel build` when the project is Vercel-compatible and local prebuild deployment is appropriate.
   - For npm-based projects, use the package manager already indicated by lockfiles and scripts.
   - For Unity WebGL or other game builds, use the repo's documented build command or existing web output directory; do not invent a Unity build pipeline from scratch.
   - If no obvious build command, output directory, or deployable root exists, stop and ask. Propose concrete options such as static site deploy, npm build setup, Unity WebGL output, or a custom command/output directory.

4. Deploy the preview.
   - If `vercel build` was used successfully, deploy with `vercel deploy --prebuilt`.
   - Otherwise use the simplest preview deployment flow supported by the project, usually `vercel` or `vercel deploy` from the deployable root.
   - Capture the deployment URL from the CLI output.

5. Report the result.
   - Include the preview URL.
   - List the build and deploy commands run.
   - Mention any first-time setup performed, such as Vercel linking.
   - If deployment fails, summarize the direct failing command and the actionable error; do not hide it behind unrelated refactors.

## Guardrails

- Keep the project-specific changes minimal. Do not add a new framework, deployment architecture, or CI workflow unless the user asks.
- Follow local tool and approval rules, especially for network access, package installation, login, and commands that write outside the workspace.
- Preserve production safety: preview is the default, production requires explicit user intent.
