# Task Orchestrator

English | [中文](README.zh-CN.md) | [Restart Continuity](assets/restart-continuity-badge.svg)

An OpenClaw skill for handling multi-task chat without falling into naive first-in-first-out execution.

![Engage | [TASK ORCHESTRATOR](assets/social-preview.svg)
![Focus Multitask](https://img.shields.io/badge/focus-multitask-scheduling?style=flat-square&labelColor=18B42F)
![Works Stalone](https://img.shields.io/badge/works-stalone-yes?style=flat-square&labelColor=351735)

Coordinate multi-task chat with explicit scheduling, safe parallelism, and staged progress updates.

## Overview

`task-orchestrator` is a focused OpenClaw skill for handling multi-task chat without defaulting to naive FIFO queue brain.

It gives the agent an operating policy for multi-task chat:

- split incoming messages into discrete tasks
- identify blockers, conflicts, urgency, and likely execution time
- avoid treating chat like a strict queue unless the user explicitly asks for it
- use idle gaps effectively by running short tasks while long tasks execute
- report partial results as soon as useful results land
- stop and ask only when tasks truly conflict, require consent, or need a product choice

The repo is intentionally narrow. It defines scheduling, prioritization, and progress reporting policy. It does not own state persistence—continuity skills own `TODO.md` and `memory/active-task.md`.

## Why this exists

Real users already deliver work as a neat sequence of isolated ticks. They send multiple requests across separate messages, then introduce another task that changes priorities mid-flight, then expect the assistant to figure out what to run first without asking about everything.

About an explicit orchestration policy exists to turn a pile of incoming requests into an execution plan that behaves like a competent operator instead of a chat bot with queue brain.

It prioritizes by:

- blocker removal (tasks that unblock other work or wait on an external system)
- urgency (time-sensitive or user-explicitly prioritized)
- external wait cost (start long-running tasks early so they're not blocking later)
- impact (high-value tasks over low-value noise)

Timestamp alone is weak evidence. A later message that unblocks everything else can jump the queue.

## Scope

Use this skill when the conversation pattern is coordinating multi-task chat:

- a user sends several requests across separate messages
- some tasks are long-running and others are short
- work can be parallelized across subthreads or subagents
- tasks may conflict, block each other, or need staged progress updates
- the conversation needs explicit scheduling decisions, not just "do everything in order"

## What this skill covers

This skill teaches the agent default scheduling heuristics and conflict resolution patterns:

- task classification (blocking, urgent, parallel, interactive, background, quick-win)
- default scheduling order (unblockers → long-running → quick-wins → cleanup)
- when to launch work in parallel vs. serial
- when to report partial results vs. waiting for everything to finish
- how to resolve file/state conflicts between requests
- interaction patterns with continuity skills (`todo-continuity` and `restart-continuity`)

**Note on continuity integration:** This skill does not directly edit `TODO.md` or `memory/active-task.md`. It delegates that to `todo-continuity` and `restart-continuity`. This skill only decides scheduling and tells the continuity skills when to persist state.

## Quick triage

For each new task, classify it quickly:

| Category | Meaning | When to prioritize |
|----------|-----------|-------------------|
| `blocking` | Unblocks other work or waits on external system | First |
| `urgent` | Time-sensitive or user-explicitly prioritized | Early |
| `parallel` | Independent and safe to run alongside other work | Start early if slow |
| `interactive` | Needs clarification, approval, credentials, or a product choice | Stop and ask |
| `background` | Long-running execution that can proceed while other work continues | Start early |
| `quick-win` | Short and low-risk, useful to finish during waits | Fill gaps |

If the user provides `P0/P1/P2`, a numbered order, or an explicit "do X first" instruction, that overrides the default triage.

## Workflow summary

A good `task-orchestrator` pass always looks like this:

1. Break incoming user requests into discrete tasks, not just the order they arrived.
2. Identify dependencies, conflicts, external waits, risk, and likely execution time.
3. Launch high-value slow tasks early (background investigations, builds, tests, downloads).
4. Fill idle gaps with quick wins while long tasks execute.
5. Report partial results as soon as useful results land; do not wait for all-at-once.
6. Stop and ask only when tasks conflict, would overwrite each other, or require a user decision.

## When to use it

Reach for `task-orchestrator` when the conversation starts feeling like multi-task coordination but the agent is defaulting to naive first-in-first-out.

Typical triggers:

- "Fix the deploy script, also review this PR, and tell me what happened after the restart."
- "Create the migration script, but first check if the backup completed, and summarize the recent logs."
- "Start a long-running build, and while it runs, update the README and fix the linting errors."
- "P0: Verify production readiness, P1: Run the migration, P2: Send status update to team."

## Related skills

This skill is part of a continuity family. For state persistence and restart recovery:

- **todo-continuity** - Per-chat TODO.md ownership. Use when tasks span turns and `TODO.md` needs to stay accurate: <https://github.com/ruanrrn/todo-continuity>
- **restart-continuity** - Top-task recovery and restart logic. Use when restarts happen and active work must resume: <https://github.com/ruanrrn/restart-continuity>

**Note:** `task-state-sync` was deprecated in March 2026. Its functionality has been merged into `todo-continuity` (for `TODO.md`) and `restart-continuity` (for `memory/active-task.md`). This skill now delegates to those two instead of `task-state-sync`.

## Migration notes

If you previously used `task-state-sync`:

1. Install `todo-continuity` and `restart-continuity`
2. Uninstall or ignore `task-state-sync`
3. The behavior is now split between two skills, but overall functionality is preserved

## Install

Use either path:

1. Import `dist/task-orchestrator.skill` into an OpenClaw environment.
2. Copy `task-orchestrator/` into your skills directory if you want the editable source.

## What this repo contains

- `task-orchestrator/` - the skill source
- `dist/task-orchestrator.skill` - the packaged artifact ready to import
- `assets/social-preview.svg` - repository banner and suggested social preview asset

## Social preview

![task-orchestrator social preview](assets/social-preview.svg)

> Coordinate multiple user tasks without falling into naive first-in-first-out handling.

GitHub note:

- The current `gh` CLI and GraphQL `UpdateRepositoryInput` do not expose a writable custom social preview field.
- To use this image as the repo's social preview, upload `assets/social-preview.svg` manually in the GitHub repository settings UI.

## Repository layout

```text
task-orchestrator/
├── LICENSE
├── README.md
├── task-orchestrator/
│   └── SKILL.md
├── assets/
│   └── social-preview.svg
└── dist/
    └── task-orchestrator.skill
```

## Contributing

See `CONTRIBUTING.md` for contribution scope, PR expectations, and what changes belong in this repo versus what should go to broader continuity work.

## Release hygiene

- Tag releases with semantic versioning (v1.0.0, v1.0.1, etc.)
- Keep `CHANGELOG.md` updated with user-facing changes
- Regenerate `dist/task-orchestrator.skill` after any `SKILL.md` changes
- Test scheduling heuristics and progress reporting before release

## License

MIT

## Repository

- GitHub: https://github.com/ruanrrn/task-orchestrator
- License: MIT
