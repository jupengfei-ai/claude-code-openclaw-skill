# Execution checklists for Claude Code via OpenClaw

Use these checklists when the base skill needs stricter completion criteria.

## UI task checklist

Use for pages, forms, dashboards, landing pages, auth screens, and visual redesigns.

Before spawning Claude Code, make sure the prompt includes:
- exact page or component to build
- whether to replace an existing page
- required fields / actions / states
- responsive requirement
- visual quality requirement
- preview URL requirement
- no remote push unless explicitly asked

Require Claude Code to return:
- status
- files changed
- validation performed
- preview URL
- blockers or follow-ups

## Bug fix checklist

Include:
- symptom
- reproduction steps or command
- suspected files if known
- root-cause expectation
- validation command

Require Claude Code to return:
- root cause
- files changed
- validation performed
- residual risk

## Repo-fit checklist

Before delegating, make the prompt tell Claude Code to:
- inspect the repo structure briefly
- follow existing project patterns where sensible
- keep edits narrowly scoped
- avoid introducing unnecessary dependencies

## Drift-recovery checklist

Use after a failed or wandering run.

Tighten all of these:
- exact visible deliverable
- minimum file scope
- no planning unless blocked
- strict output format
- mandatory verification

Good signs of drift:
- Claude keeps analyzing instead of editing
- it edits the wrong page or wrong feature
- it reports success without a preview URL
- it leaves the original page intact

## Recommended output contracts

### UI contract

```text
Output:
- status
- files changed
- validation performed
- preview URL
- blockers or follow-ups
```

### Code contract

```text
Output:
- status
- files changed
- validation performed
- blockers or follow-ups
```

### Bug-fix contract

```text
Output:
- root cause
- files changed
- validation performed
- residual risk
```
