# Claude Code official docs distilled for OpenClaw

This note distills the most useful parts of the official Claude Code docs for an OpenClaw operator who wants to delegate work well.

Primary sources:
- Overview: https://code.claude.com/docs/en/overview
- Best practices: https://code.claude.com/docs/en/best-practices
- Common workflows: https://code.claude.com/docs/en/common-workflows
- Extend Claude Code: https://code.claude.com/docs/en/features-overview
- Memory / CLAUDE.md: https://code.claude.com/docs/en/memory
- Permissions: https://code.claude.com/docs/en/permissions
- Skills: https://code.claude.com/docs/en/skills
- Subagents: https://code.claude.com/docs/en/sub-agents
- Hooks guide: https://code.claude.com/docs/en/hooks-guide
- CLI reference: https://code.claude.com/docs/en/cli-reference
- Headless / print mode: https://code.claude.com/docs/en/headless

## 1) Core operating model

Claude Code is best treated as an agentic coding worker that can read code, edit files, run commands, and verify its own output.

The official docs repeatedly push the same pattern:
1. give Claude concrete context
2. give it a way to verify its work
3. separate exploration from implementation on complex tasks
4. keep sessions focused
5. isolate noisy research into subagents or separate sessions

For OpenClaw delegation, this means prompts should include:
- working directory or repo
- explicit goal
- constraints
- verification commands or preview expectations
- output format requirements

## 2) Highest-leverage prompting rules

From the best-practices and common-workflows docs, these matter most:

- **Verification is king.** Tests, screenshots, preview URLs, build commands, or exact success criteria sharply improve results.
- **Explore -> plan -> implement** is best for unfamiliar or risky work.
- **Be specific.** Mention relevant files, directories, bugs, constraints, and patterns to copy.
- **Course-correct early.** If a session drifts, restart with a cleaner prompt instead of piling on corrections.
- **Use fresh sessions when context is polluted.** The docs explicitly warn against “kitchen sink sessions.”

## 3) Plan Mode vs normal execution

Official guidance:
- Use Plan Mode for multi-file changes, architecture work, or safe analysis.
- Use normal execution when the task is straightforward and implementation should begin immediately.
- Plan Mode is analysis-only; it is not the right default for every small task.

OpenClaw implication:
- For repo exploration, migrations, auth changes, architecture, or “first understand then implement,” ask Claude Code to inspect first and produce a short plan.
- For narrow UI tasks, bug fixes, or contained edits, tell it to implement directly.

## 4) CLAUDE.md, auto memory, and skills

Official feature layering:
- **CLAUDE.md** = always-on project instructions
- **Skills** = on-demand knowledge or workflows
- **Subagents** = isolated workers
- **Hooks** = deterministic automation
- **MCP** = external tools and services

Important distinction:
- Put permanent project rules in `CLAUDE.md`.
- Put reusable, on-demand workflows in skills.
- Put heavy investigation into subagents.

OpenClaw implication:
- If a user just wants Claude Code to do coding work, do not dump massive reusable guidance into every prompt.
- Keep the router skill short and load references only when needed.

## 5) Permissions and safe autonomy

Official docs define several permission modes, including `plan`, `acceptEdits`, and `bypassPermissions`.

Practical guidance from the docs:
- `bypassPermissions` is only suitable in isolated environments.
- Fine-grained tool approval rules are preferred when safety matters.
- Hooks can enforce policy before or after tool use.

OpenClaw implication:
- In ACP / OpenClaw delegation, use safe but explicit task framing.
- Ask Claude Code to avoid remote pushes unless the user explicitly requested them.
- Require validation before reporting success.

## 6) Hooks and deterministic automation

Official docs recommend hooks when you need predictable enforcement or automation, such as:
- run formatting after edits
- block protected file edits
- verify work before stop
- emit notifications

OpenClaw implication:
- If future Claude Code behavior must be reliable across runs, prefer hooks or project config over repeating the same prompt instruction forever.
- This skill should mention hooks as a follow-up option, not embed hook policy by default.

## 7) Subagents and parallel work

Official docs recommend subagents when:
- the task is verbose
- you need context isolation
- research should not pollute the main session
- you want parallel investigation

OpenClaw implication:
- For a single user request to “use Claude Code,” start with one Claude Code session.
- For larger work, ask Claude Code inside the prompt to use subagents for investigation if useful.
- Do not make subagents mandatory for every task.

## 8) Headless / CLI patterns that matter

The headless docs emphasize:
- `claude -p` for non-interactive one-shot work
- structured outputs when automation needs machine-readable results
- continuing or resuming sessions when continuity matters

OpenClaw implication:
- ACP session `mode: run` maps well to one-shot jobs.
- ACP session `mode: session` maps well to iterative work.
- Prompts should ask for concise structured summaries: changed files, validation, preview URL, blockers.

## 9) Recommended OpenClaw prompt shape

Use this order:
1. objective
2. repo / cwd
3. context or relevant files
4. constraints
5. verification
6. output format

Good example skeleton:

```text
Work in /path/to/repo.

Objective: implement X.

Context:
- inspect relevant files first
- follow existing patterns in Y and Z

Constraints:
- do not push remotely
- keep changes focused
- use English in code comments and summary

Verification:
- run npm test
- run npm run build
- for UI work, start the app and provide a preview URL

Output:
- status
- files changed
- validation performed
- preview URL or blocker
```

## 10) When to restart instead of continuing

The official docs strongly prefer a fresh session when:
- Claude got corrected several times already
- context contains dead-end attempts
- the current session is doing unrelated work
- the user wants a very different task style (for example, from exploration to urgent implementation)

OpenClaw implication:
- If a Claude Code run does not land changes after one or two clear prompts, start a new run with a narrower brief.
- Do not keep stacking vague follow-ups into the same failing context.

## 11) Guidance for this skill

This skill should help OpenClaw do three things well:
- choose when to use Claude Code
- construct strong prompts grounded in the docs
- route work through OpenClaw ACP sessions with clear verification and reporting requirements

Keep the skill body short. Put detailed examples and templates in separate references.
