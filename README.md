# Task Orchestrator

English | [简体中文](README.zh-CN.md)

![Task Orchestrator banner](assets/social-preview.svg)

![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-132A13?style=flat-square)
![Focus-Multitask Orchestration](https://img.shields.io/badge/Focus-Multitask%20Orchestration-DDA15E?style=flat-square&labelColor=132A13)
![Works-Standalone](https://img.shields.io/badge/Works-Standalone-E9EDC9?style=flat-square&labelColor=31572C)
![Artifact-.skill Included](https://img.shields.io/badge/Artifact-.skill%20Included-B7C89A?style=flat-square&labelColor=31572C)
![README-Bilingual](https://img.shields.io/badge/README-Bilingual-E9EDC9?style=flat-square&labelColor=6B705C)
![License-MIT](https://img.shields.io/badge/License-MIT-E9EDC9?style=flat-square&labelColor=132A13)

Coordinate multi-request chat work with explicit prioritization, safe parallelism, and staged progress reporting.

## Overview

`task-orchestrator` is a focused OpenClaw skill for handling several user requests inside the same conversation without defaulting to naive first-in-first-out execution.

It gives the agent an operating policy for multitask chat work:

- split incoming messages into discrete tasks
- identify blockers, conflicts, urgency, and wait costs
- start the right long-running work early
- use idle windows for short, low-risk tasks
- report partial results before the entire bundle is complete

The repository is intentionally narrow. It defines orchestration behavior, not general project management, and not persistence or restart recovery by itself.

## Why this exists

Real users rarely deliver work as a neat sequence of isolated tickets. They send one request, then add another, then introduce a third message that changes the priority of the first two.

Without an explicit orchestration policy, agents tend to fail in familiar ways:

- long tasks block short ones for no good reason
- important unblockers wait behind lower-value work
- independent tasks never get parallelized
- progress goes silent while background work runs
- the conversation turns into a queue instead of an operating plan

`task-orchestrator` exists to replace that failure mode with a clear default scheduling model.

## Scope

Use this skill when the problem is coordinating multiple user tasks in one active conversation.

Good fit:

- several requests arrive across separate messages
- some work is short while other work is long-running
- multiple tasks can run safely in parallel
- requests may conflict, block each other, or require staged reporting
- the agent needs to decide what to launch now versus what to defer

Not a fit:

- single-task turns that do not need orchestration
- continuity storage by itself
- restart recovery by itself
- general workflow automation outside the chat task bundle

## What the skill covers

This skill teaches the agent to standardize the parts of multitask execution that most often go wrong:

- classify incoming work by urgency, blockers, and runtime profile
- avoid strict arrival-order handling unless the user explicitly asks for it
- keep the main thread focused on orchestration, communication, and decisions
- offload slower execution to subthreads or subagents when useful
- stop at genuine conflicts instead of guessing
- surface useful partial results as soon as they are ready
- cooperate with continuity skills when the task bundle spans turns or restarts

## Workflow summary

A good `task-orchestrator` pass usually looks like this:

1. Break the incoming messages into real tasks.
2. Identify dependencies, conflicts, risk, and likely runtime.
3. Launch high-value long-running work early.
4. Use wait windows for short, safe wins.
5. Report meaningful partial progress instead of waiting for a final bundle.
6. Repair or escalate state conflicts before they spread.

## When to use it

Reach for `task-orchestrator` when the conversation starts acting like a live queue instead of a single request.

Typical triggers:

- "Fix the config bug, then summarize this log, and also start a PR review."
- "Handle these two issues in parallel, but ask before touching shared files."
- "Keep me posted as each piece finishes instead of waiting for the whole batch."
- "We may need to restart midway, so keep the task ordering sane."

## Representative outcomes

### Mixed workload coordination

A user sends a blocker investigation, a documentation request, and a longer review task in quick succession.

A good agent should start the blocker check immediately, launch the longer lane early if it can run independently, finish the quick documentation work during wait time, and report each useful result as it lands.

### Conflict-aware scheduling

Two requests touch the same files or impose incompatible constraints.

A good agent should stop at the real decision point, ask which instruction wins, and avoid making a confident mess.

### Restart-aware cooperation

A multitask bundle is likely to survive a restart or session reset.

A good agent should keep orchestration policy here while coordinating with continuity skills that store unfinished queues and the top active task.

## Related skill repos

These repositories are related examples, not required dependencies:

- `task-state-sync`: companion repo for keeping continuity files accurate while multitask work is in flight - <https://github.com/ruanrrn/task-state-sync>
- `multi-task-continuity`: umbrella repo that combines orchestration, state sync, and restart-safe recovery into one operating model - <https://github.com/ruanrrn/multi-task-continuity>

Start with this repo when the problem is task ordering, parallel execution, and progress reporting inside the conversation itself.
Use the umbrella repo when you want the broader continuity workflow around that orchestration policy.

## Install

Use either path:

1. Import `dist/task-orchestrator.skill` into an OpenClaw environment.
2. Copy `task-orchestrator/` into your skills directory if you want the editable source.

## What this repo contains

- `task-orchestrator/` - the skill source
- `dist/task-orchestrator.skill` - the packaged artifact ready to import
- `assets/social-preview.svg` - the repository banner and suggested social-preview asset
- `CONTRIBUTING.md` - contribution scope and repo-specific workflow guidance

## Social preview

Suggested social preview asset: `assets/social-preview.svg`

Suggested one-line copy:

> Coordinate multi-request chat work with explicit prioritization, safe parallelism, and staged progress reporting.

GitHub note:

- The current `gh` CLI and GraphQL `UpdateRepositoryInput` do not expose a writable custom social preview field.
- To use this image as the repository social preview, upload `assets/social-preview.svg` manually in the repo settings UI.

## Repository layout

```text
task-orchestrator/
├── LICENSE
├── README.md
├── README.zh-CN.md
├── CONTRIBUTING.md
├── assets/
│   └── social-preview.svg
├── task-orchestrator/
│   └── SKILL.md
└── dist/
    └── task-orchestrator.skill
```

## Contributing

See `CONTRIBUTING.md` for contribution scope, packaging expectations, and the boundary that keeps this repository focused on multitask orchestration rather than absorbing adjacent continuity logic.

## Release hygiene

- regenerate `dist/task-orchestrator.skill` after each material skill change
- keep `README.md`, `README.zh-CN.md`, `task-orchestrator/SKILL.md`, and repository metadata aligned
- keep this repository focused on orchestration policy instead of broad continuity features
- refresh representative outcomes when the scheduling policy changes materially

## Repository

- GitHub: `https://github.com/ruanrrn/task-orchestrator`
- License: MIT
