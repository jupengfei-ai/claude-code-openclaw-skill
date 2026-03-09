---
name: claude-code-openclaw
description: Route user requests that explicitly ask for Claude Code, Claude Code CLI, Claude-specific plan mode / CLAUDE.md / hooks / subagents / MCP workflows, or docs-grounded Claude Code delegation. Use when OpenClaw should send work to Claude Code through ACP sessions and the prompt should follow official Claude Code best practices.
---

# Claude Code for OpenClaw

Use this skill when the user explicitly wants Claude Code, or when Claude-specific workflows matter more than generic delegation.

## Quick start

1. Use OpenClaw ACP sessions, not local PTY scraping.
2. Set `agentId` to `claude`.
3. Use `mode: "run"` for one-shot work and `mode: "session"` for iterative work.
4. Write a repo-aware prompt with:
   - objective
   - working directory
   - constraints
   - verification
   - required output format
5. Ask for concrete validation: tests, build, screenshot, or preview URL.
6. If the task is complex, tell Claude Code to inspect first and produce a short plan before editing.
7. If a prior Claude Code run drifted, start a fresh run with a narrower prompt instead of piling on corrections.

## Prompting rules from the official docs

Follow these rules unless the user asks otherwise:

- Give Claude Code a way to verify its own work.
- Prefer explore -> plan -> implement for risky or unfamiliar tasks.
- Be specific about files, directories, symptoms, constraints, and expected outputs.
- Keep sessions focused; use a fresh run when context is polluted.
- For UI tasks, require a running preview URL and visual verification when possible.
- For coding tasks, forbid remote pushes unless the user explicitly asked.

If you need the rationale or examples, read:
- `references/official-docs-distilled.md`
- `references/prompt-templates.md`

## Mode selection

### Use `mode: "run"`
Use for:
- one-shot implementation
- narrow bug fixes
- quick reviews
- isolated analysis tasks

### Use `mode: "session"`
Use for:
- back-and-forth implementation
- long-running refactors
- work that will need follow-up turns
- thread-style collaboration

## Prompt patterns

### Direct implementation

Use when the task is contained and should begin immediately.

```text
Work in /path/to/repo.

Objective: <goal>.

Implement immediately.

Constraints:
- keep the change focused
- follow existing patterns
- do not push remotely unless explicitly asked

Validation:
- run <commands>
- for UI work, provide a preview URL

Output:
- status
- files changed
- validation performed
- blockers or follow-ups
```

### Explore first, then implement

Use when the task is broad, risky, or architecture-heavy.

```text
Work in /path/to/repo.

Objective: <goal>.

First inspect the relevant files and explain the current structure briefly.
Then propose a short implementation plan.
After that, implement the change, run validation, and summarize the result.
```

### Recovery prompt for a drifting run

Use when a previous Claude Code attempt failed to land the change.

```text
URGENT. Work in /path/to/repo.

You are only allowed to touch the minimum files required for this task.
Task: <exact deliverable>

Strict requirements:
- do not stop at analysis
- do not return a plan unless blocked
- do the implementation now
- verify the result
- do not push remotely

Return exactly:
- status
- files changed
- validation
- preview URL or blocker
```

For fuller templates, read `references/prompt-templates.md`.

## OpenClaw routing notes

- Prefer `sessions_spawn` with `runtime: "acp"`.
- Use `agentId: "claude"`.
- Set `cwd` explicitly.
- Do not route Claude Code work through `subagents` or local terminal hacks when ACP is available.
- On ACP policy failure, surface the policy error clearly.

### Good spawn shape

```json
{
  "runtime": "acp",
  "agentId": "claude",
  "mode": "run",
  "cwd": "/path/to/repo",
  "task": "<prompt>"
}
```

## When to read extra references

Read `references/official-docs-distilled.md` when:
- the user asks for Claude Code best practices
- you need to decide between skills, CLAUDE.md, hooks, or subagents
- you need stronger docs-grounded prompt construction
- you are creating or refining Claude Code workflows

Read `references/prompt-templates.md` when:
- you need a ready-made prompt skeleton
- the task is UI, bug fix, refactor, or review
- a previous Claude Code run drifted and needs a reset

## Output expectations back to the user

After Claude Code finishes, report only what matters:
- what was done
- what passed or failed
- key files changed
- preview URL or validation commands
- next step if blocked

Do not dump raw orchestration details unless the user asks.
