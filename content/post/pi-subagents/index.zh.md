---
title: "pi-subagents 扩展使用说明"
description: "介绍 Pi 子代理的角色、链式与并行工作流，以及如何控制任务边界。"
date: "2026-08-01"
slug: "pi-subagents"
categories: [AI 工具]
tags: ["Pi","子代理","扩展"]
image: "/img/article-covers/pi-subagents.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：0.38.0 ｜ 许可：MIT

## 工具介绍

pi-subagents 让 Pi 把任务委托给**聚焦的子代理**（独立的子 Pi 会话）：代码审查、侦察、实现、并行审计、后台任务等。父会话负责调度，子代理专注执行并把结果带回。

- 内置 8 个角色：`scout`（代码侦察）、`researcher`（资料研究）、`planner`（只读规划）、`worker`（实现）、`reviewer`（审查）、`context-builder`（上下文收集）、`oracle`（第二意见）、`delegate`（通用轻量代理）
- 前台流式 / 后台运行；支持链式（`->`）、并行（`||` 或括号分组）、多轮 review loop
- 安装即用，自然语言触发；也提供精确的 `/run`、`/chain`、`/parallel` 命令
- 子代理模型默认继承当前 Pi 默认模型，可单独覆盖；可选 watchDog 对抗性变更审查（推荐用互补的强模型）
- 可选 worktree 隔离、pi-permission-system 集成、会话分享、递归保护

来源：GitHub `nicobailon/pi-subagents`（**2,810★，pi 生态星标第一**，活跃维护），pi.dev 下载量约 166K/月。

## 安装

```bash
pi install npm:pi-subagents
```

## 卸载

```bash
pi remove npm:pi-subagents
```

## 基本使用

安装后**无需任何配置**，直接用自然语言：

```text
Use reviewer to review this diff.
Ask oracle for a second opinion on my current plan.
Use scout to understand this code, then ask me clarification questions.
Run parallel reviewers: one for correctness, one for tests, one for unnecessary complexity.
```

精确命令（可选）：

| 命令 | 作用 |
|---|---|
| `/run <agent> [task]` | 运行单个代理 |
| `/chain a "t1" -> b "t2"` | 链式执行；`(x \| y)` 括号内并行 |
| `/parallel a "t1" -> b "t2"` | 并行执行 |
| `/run-chain <chainName> -- <task>` | 启动保存的 `.chain.md` 工作流 |
| `/subagents [agent] [model\|thinking\|prompt\|details]` | 交互式查看/修改代理的模型、思考级别、提示词 |
| `/subagents-models [agent]` | 查看运行时模型映射 |
| `/subagent-cost` | 查看父子代理 token 与费用 |
| `/subagents-doctor` | 只读安装诊断 |
| `/subagents-watchdog [status\|on\|off\|model ...]` | 可选 watchdog 变更审查（推荐 Opus 4.8 或 GPT 5.5 级别强模型） |

## 配置

写入 `~/.pi/agent/settings.json`（用户级）或 `.pi/settings.json`（项目级）：

```json
{
  "subagents": {
    "defaultModel": "deepseek-v4-flash",
    "defaultThinking": "medium",
    "agentOverrides": {
      "reviewer": { "model": "anthropic/claude-sonnet-4", "thinking": "high" }
    }
  }
}
```

要点：

- `defaultModel`/`defaultThinking` 作用于未显式设置的内置代理；`agentOverrides.<name>` 更具体且优先
- 模型 ID 支持模糊匹配（分隔符、大小写、日期后缀差异都能解析）
- 中转站/第三方 provider 若拒绝带 thinking 后缀的模型 ID，设 `"disableThinking": true`
- `projectRootResolution: "git-root"` 可在 monorepo/worktree 中锚定仓库根

## 注意事项

- **子代理是独立会话，token 消耗成倍增加**——按量计费（中转站）用户注意用量，可用 `/subagent-cost` 监控
- 子代理与主代理共享权限面；`worker` 会改文件，watchdog 默认关闭需显式开启
- 运行痕迹与产物在 `~/.pi/agent/profiles/pi-subagents/` 与工作区 `.pi-subagents/`

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-subagents\README.md`（1,873 行，含链式语法、工作流隔离等进阶内容）
- GitHub：https://github.com/nicobailon/pi-subagents
