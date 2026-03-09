# Prompt templates for Claude Code via OpenClaw

Use these as building blocks. Keep them concrete and repo-aware.

## 1) Explore first, then implement

```text
Work in /path/to/repo.

Objective: <goal>.

First inspect the relevant files and explain the current structure briefly.
Then propose a short implementation plan.
After that, implement the change, run the relevant validation, and summarize the result.

Constraints:
- keep changes focused
- do not push remotely unless explicitly asked

Validation:
- run <commands>

Output:
- status
- files changed
- validation performed
- blockers or follow-ups
```

## 2) Direct implementation

```text
Work in /path/to/repo.

Objective: <goal>.

Implement immediately.

Constraints:
- follow existing project patterns
- keep the change narrow
- use English in code comments and status text
- do not push remotely unless explicitly asked

Validation:
- run <commands>

Output:
- status
- files changed
- validation performed
- any remaining limitations
```

## 3) UI / preview task

```text
Work in /path/to/repo.

Objective: build or update <screen/component>.

Requirements:
- responsive
- polished visuals
- appropriate states and basic interactions
- fit the existing project structure

Validation:
- run the local app or preview server
- avoid port conflicts
- provide the final preview URL
- if possible, verify visually

Output:
- status
- files changed
- validation performed
- preview URL
```

## 4) Bug fix

```text
Work in /path/to/repo.

Objective: fix <bug>.

Context:
- symptom: <symptom>
- suspected area: <files/dirs>
- reproduction: <steps or command>

Requirements:
- identify root cause, not a superficial patch
- add or update tests when appropriate

Validation:
- reproduce the issue first if possible
- run <commands>

Output:
- root cause
- files changed
- validation performed
- residual risk
```

## 5) Refactor

```text
Work in /path/to/repo.

Objective: refactor <area>.

First inspect the current implementation and summarize constraints.
Then produce a short plan.
Then implement the refactor without changing behavior unless explicitly requested.

Validation:
- run tests and type checks

Output:
- status
- files changed
- validation performed
- notable tradeoffs
```

## 6) Code review / audit

```text
Review the relevant changes in /path/to/repo.

Focus on:
- correctness
- regressions
- edge cases
- maintainability
- security when relevant

Output:
- prioritized findings only
- file references
- concrete fixes or recommendations
```

## 7) Strong fallback prompt for a drifting session

Use this when a previous Claude Code run wandered or failed to land changes.

```text
URGENT. Work in /path/to/repo.

You are only allowed to touch the minimum files required for this task.

Task:
<exact deliverable>

Strict requirements:
- do not stop at analysis
- do not return a plan unless blocked
- do the implementation now
- do not push remotely

Validation:
- verify the result in the running app or with the listed commands

Return exactly:
- status
- files changed
- validation
- preview URL or blocker
```
