# Task Orchestrator

[English](README.md) | 简体中文

![Task Orchestrator banner](assets/social-preview.svg)

![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-132A13?style=flat-square)
![Focus-Multitask Orchestration](https://img.shields.io/badge/Focus-Multitask%20Orchestration-DDA15E?style=flat-square&labelColor=132A13)
![Works-Standalone](https://img.shields.io/badge/Works-Standalone-E9EDC9?style=flat-square&labelColor=31572C)
![Artifact-.skill Included](https://img.shields.io/badge/Artifact-.skill%20Included-B7C89A?style=flat-square&labelColor=31572C)
![README-Bilingual](https://img.shields.io/badge/README-Bilingual-E9EDC9?style=flat-square&labelColor=6B705C)
![License-MIT](https://img.shields.io/badge/License-MIT-E9EDC9?style=flat-square&labelColor=132A13)

协调多个用户任务，不再掉进天真的先进先出处理坑里。

## Quick pitch

给多请求工作提供更聪明的调度、优先级判断和分阶段进度反馈。
当聊天不再是一件事，而是开始像一个待办队列时，就该用它了。

## Why this exists

用户不会像提交工单那样，整整齐齐一次只发一件事。他们会先丢一个任务，再补一个，然后第三条消息悄悄把前两件事的优先级一起改了。如果代理还按傻乎乎的 FIFO 队列处理，结果基本固定：长任务卡死短任务，真正的 blocker 等太久，进度悄无声息地消失，重启以后计划也散一地。

`task-orchestrator` 就是来收拾这个烂摊子的。

它给代理提供一套处理多请求聊天工作的默认执行策略：

- 先把连续消息拆成真正的离散任务
- 识别依赖、冲突、紧急程度和外部等待成本
- 尽早启动高价值的长耗时工作
- 在等待窗口里塞进短平快任务
- 只要有独立有用的结果，就立刻分阶段汇报
- 当工作会跨轮次或跨重启时，与连续性状态文件协作

这不是给项目经理 cosplay 用的提示词。这是一套紧凑的执行政策，让代理别再长得像一个只有队列脑的聊天机器人。

## Works independently

`task-orchestrator` 单独使用就很有价值。

就算你一个配套技能都不用，它也已经能改善：

- 任务分诊
- 排序与优先级判断
- 安全并行执行
- 进度汇报
- 冲突处理

配套技能会让持久化和重启恢复更强，但它们是可选增强，不是藏起来的前置依赖。

## What the skill teaches

这个技能会告诉代理：

- 除非用户明确要求，否则不要死守到达顺序
- 以解除阻塞、紧急程度、影响范围和运行时收益来排序
- 让主线程专注在编排、用户沟通和决策上
- 把更慢或更重复的执行下放到子线程或 subagent
- 只有在真正的决策点才停下来问，不要习得性无助
- 当工作跨轮次或跨重启时，保持连续性状态同步

## When to use it

在这些场景里用 `task-orchestrator`：

- 用户跨多条消息发来多个任务
- 有些任务很快，有些任务会跑很久
- 工作可以安全并行
- 任务之间可能冲突，或者彼此阻塞
- 代理需要分阶段主动汇报进度
- 这一组任务可能跨 session reset 或 gateway restart

## Example behavior

### Example 1: 混合工作负载

用户发送：

- "Fix the config bug"
- "Also summarize this log"
- "And start a PR review"

一个靠谱代理应该：

1. 先判断 config bug 是否是 blocker
2. 如果合适，把 PR review 作为后台工作启动
3. 在长任务等待期间顺手完成日志总结
4. config 结果一出来就立刻汇报，而不是坐着憋总包

### Example 2: 冲突任务

用户发送：

- "Refactor this module"
- "Do not change the public API yet"
- "Also rename the exported functions"

一个靠谱代理应该在冲突处停下，问清楚哪条指令优先，而不是自信地把现场弄成废墟。

### Example 3: 重启感知的多任务处理

用户有多项进行中的任务，这时 gateway 需要重启。

一个靠谱代理应该：

1. 把当前聊天的未完成任务队列写进 `TODO.md`
2. 把当前最重要的任务写进 `memory/active-task.md`
3. 如果重启是计划内的，先安排重启后的兜底唤醒
4. 重启后优先恢复顶部任务
5. 主动告诉用户恢复了什么，而不是等用户催

## Related skills

这些有关联，但不是必需品：

- `task-state-sync`: 在多任务执行过程中保持连续性文件准确 - <https://github.com/ruanrrn/task-state-sync>
- `multi-task-continuity`: 把编排、状态同步和重启恢复打包成一个总流程 - <https://github.com/ruanrrn/multi-task-continuity>

如果你只需要调度脑，而不是整套连续性系统，这个仓库单独用就够了。

## Social preview

建议使用的社交预览图：`assets/social-preview.svg`

建议的一句话文案：

> Smart scheduling, prioritization, and staged progress for multi-request work.

GitHub 说明：

- 当前 `gh` CLI 和 GraphQL `UpdateRepositoryInput` 都不提供可写的自定义 social preview 字段。
- 如果要把这张图用作仓库的 social preview，需要到仓库设置页面手动上传 `assets/social-preview.svg`。

## What you get

- `task-orchestrator/` - 技能源码
- `dist/task-orchestrator.skill` - 可直接导入的打包产物

## Install

两种方式都可以：

1. 把 `dist/task-orchestrator.skill` 导入 OpenClaw 环境。
2. 如果你要可编辑源码，就把 `task-orchestrator/` 复制到你的 skills 目录。

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

见 `CONTRIBUTING.md`，里面写了这个仓库接受什么改动、PR 该交代什么，以及怎么避免把这个仓库养成一个什么都想塞的杂物间。

## Release hygiene

- 只要技能有实质变更，就重新生成 `dist/task-orchestrator.skill`
- 保持仓库只聚焦这个技能本身
- 仓库 description 要和 `SKILL.md` 的触发语言一致
- 编排策略明显变化时，别忘了更新示例

## Repository

- GitHub: `https://github.com/ruanrrn/task-orchestrator`
- License: MIT
