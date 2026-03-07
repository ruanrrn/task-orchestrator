# Task Orchestrator

A reusable OpenClaw skill for handling multiple user requests like a competent operator instead of a FIFO queue with delusions of grandeur.

## Why this exists

Chat surfaces make it easy for users to send several tasks across separate messages, often with very different runtimes and dependencies. A naive assistant handles those requests in raw arrival order, waits silently on long work, and turns simple multitasking into avoidable chaos.

`task-orchestrator` turns that mess into an execution policy. It teaches an agent to split incoming requests into discrete tasks, identify dependencies and conflicts, launch independent long-running work early, use wait windows for quick wins, and report partial progress as soon as it is useful.

This version also integrates with continuity-oriented workspace patterns. Multi-task work should survive restarts and session resets without losing the plot, so the skill explicitly defines how to cooperate with `todo-continuity` and `restart-continuity`.

## What you get

- `task-orchestrator/` - the skill source
- `dist/task-orchestrator.skill` - packaged artifact ready to import

## Install

Use either path:

1. Import `dist/task-orchestrator.skill` into an OpenClaw environment.
2. Copy `task-orchestrator/` into your skills directory if you want the editable source.

## When to use the skill

Use it when an agent needs to coordinate more than one task intelligently, especially when:

- a user sends several requests across separate messages
- some tasks are long-running while others are short
- work can be parallelized across subthreads or subagents
- tasks may conflict, block each other, or need staged progress updates
- work should survive restarts without losing the active plan

## Skill outcome

The skill gives the agent a default multitask policy:

- split incoming requests into real tasks
- classify them by urgency, blocker value, parallel safety, and runtime
- start high-value long work early
- use slack time for quick wins
- report partial results as they land
- stop and ask when requests truly conflict
- keep `TODO.md` and `memory/active-task.md` aligned when continuity matters

## Repository layout

```text
task-orchestrator/
├── LICENSE
├── README.md
├── task-orchestrator/
│   └── SKILL.md
└── dist/
    └── task-orchestrator.skill
```

## Release hygiene

- Regenerate `dist/task-orchestrator.skill` after each material change to the skill
- Keep the repository focused on this single skill
- Keep the trigger language in `SKILL.md` aligned with the repository description

## Repository

- GitHub: `https://github.com/ruanrrn/task-orchestrator`
- License: MIT
