---
name: claude-code-openclaw
description: Strong Claude Code delegation for OpenClaw. Use when the user explicitly says Claude Code / Claude Code CLI, or phrases like “用 Claude Code 做这个”, “让 Claude Code 实现/修复/重构/运行/预览”. Routes work through ACP with English prompts, stricter verification, preview URL requirements for UI work, and reset strategies for drifting Claude Code runs.
---

# Claude Code for OpenClaw

Use this skill when the user explicitly wants Claude Code, or when Claude-specific workflows matter more than generic delegation.

## Core rule

Route through OpenClaw ACP sessions.

- Use `sessions_spawn`
- Set `runtime: "acp"`
- Set `agentId: "claude"`
- Set `cwd` explicitly
- Prefer `mode: "run"` for one-shot tasks
- Prefer `mode: "session"` for iterative work

Do not use local PTY scraping or ad-hoc terminal control when ACP is available.

## Default execution workflow

For most Claude Code tasks, follow this sequence:

1. Identify the real deliverable.
2. Decide whether the task is direct-implementation or explore-first.
3. Write the task prompt in English.
4. Include repo path, constraints, verification, and output format.
5. Require Claude Code to actually run or validate the result.
6. After completion, report only the user-relevant outcome.

## Task classification

### Direct implementation

Use when the task is narrow and the deliverable is clear.

Examples:
- build a login page
- fix a specific bug
- add tests for one module
- refactor a contained component

Prompt shape:
- objective
- required features
- constraints
- validation
- output format

### Explore first, then implement

Use when the task is broad, risky, or architecture-heavy.

Examples:
- authentication redesign
- multi-file migrations
- unfamiliar subsystem changes
- “first understand the repo” requests

Prompt shape:
- inspect relevant files first
- summarize current structure briefly
- produce a short plan
- then implement
- then validate

## Non-negotiable prompting rules

Unless the user overrides them, do all of the following:

- Write prompts in English.
- Name the working directory explicitly.
- Give Claude Code a way to verify its work.
- For UI work, require a preview URL.
- For bug fixes, ask for root-cause validation when possible.
- For coding work, forbid remote pushes unless the user explicitly asked.
- Require a concise final report with status, changed files, validation, and blockers.

## UI task rules

For UI, page, component, landing page, dashboard, form, or login screen work:

- Require a visually identifiable result, not just code edits.
- Require the app to be run or reused if already running.
- Require a preview URL.
- Require responsive behavior.
- Require the current incorrect page to be replaced if the user asked for replacement.

Use this output contract:

```text
Output:
- status
- files changed
- validation performed
- preview URL
- blockers or follow-ups
```

If you need more examples, read `references/execution-checklists.md` and `references/prompt-templates.md`.

## Strong prompt patterns

### 1) Direct implementation

```text
Work in /path/to/repo.

Objective: <goal>.

Implement immediately.

Requirements:
- <features>

Constraints:
- keep the change focused
- follow existing project patterns where sensible
- do not push remotely unless explicitly asked
- use English for comments and status text

Validation:
- run <commands>
- if this is UI work, run or reuse the local app and provide the preview URL

Output:
- status
- files changed
- validation performed
- preview URL or blocker
```

### 2) Explore first, then implement

```text
Work in /path/to/repo.

Objective: <goal>.

First inspect the relevant files and explain the current structure briefly.
Then propose a short implementation plan.
After that, implement the change, run validation, and summarize the result.
```

### 3) Recovery prompt for a drifting run

Use this when Claude Code failed once already, produced the wrong page, or kept planning without landing changes.

```text
URGENT. Work in /path/to/repo.

You are only allowed to touch the minimum files required for this task.
Task: <exact deliverable>

Strict requirements:
- do not stop at analysis
- do not return a plan unless blocked
- do the implementation now
- verify the result in the running app or with the listed commands
- do not push remotely

Return exactly:
- status
- files changed
- validation
- preview URL or blocker
```

## Spawn patterns

### One-shot

```json
{
  "runtime": "acp",
  "agentId": "claude",
  "mode": "run",
  "cwd": "/path/to/repo",
  "task": "<prompt>"
}
```

### Iterative session

```json
{
  "runtime": "acp",
  "agentId": "claude",
  "mode": "session",
  "cwd": "/path/to/repo",
  "task": "<prompt>"
}
```

## Failure handling

If a Claude Code run drifts:

1. Do not keep stacking vague follow-ups.
2. Start a fresh run with a narrower prompt.
3. Limit the allowed scope in the prompt.
4. Restate the exact visible deliverable.
5. Force structured output.

If policy blocks `agentId: "claude"`, surface the exact policy error.

## Reporting back to the user

After Claude Code finishes, report only what matters:

- what was done
- what passed or failed
- key files changed
- preview URL or validation commands
- next step if blocked

Do not dump raw orchestration details unless the user asks.

## Read these references only when needed

- Read `references/official-docs-distilled.md` when you need official-docs rationale.
- Read `references/prompt-templates.md` when you need reusable prompt skeletons.
- Read `references/execution-checklists.md` when the task is UI-heavy, drift-prone, or needs stricter completion criteria.
