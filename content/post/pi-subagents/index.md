---
title: "pi-subagents Extension Guide"
description: "Pi subagent roles, chained and parallel workflows, and how to control task boundaries."
date: "2026-08-01"
slug: "pi-subagents"
categories: [AI 工具]
tags: ["Pi","Subagents","Extension"]
image: "/img/article-covers/pi-subagents.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 0.38.0 | License: MIT

## Introduction

pi-subagents lets Pi delegate work to **focused subagents** (separate sub-Pi sessions): code review, reconnaissance, implementation, parallel auditing, background tasks, and more. The parent session orchestrates; subagents focus on execution and bring results back.

- 8 built-in roles: `scout` (code reconnaissance), `researcher` (documentation research), `planner` (read-only planning), `worker` (implementation), `reviewer` (review), `context-builder` (context gathering), `oracle` (second opinion), `delegate` (generic lightweight agent)
- Foreground streaming / background runs; supports chains (`->`), parallel (`||` or parenthesized groups), and multi-round review loops
- Works out of the box with natural language triggers; precise `/run`, `/chain`, `/parallel` commands also available
- Subagent models inherit Pi's current default model unless overridden; optional watchdog for adversarial change review (a strong complementary model is recommended)
- Optional worktree isolation, pi-permission-system integration, session sharing, and recursion protection

Source: GitHub `nicobailon/pi-subagents` (**2,810★, #1 by stars in the Pi ecosystem**, actively maintained), pi.dev ~166K downloads/month.

## Installation

```bash
pi install npm:pi-subagents
```

## Uninstallation

```bash
pi remove npm:pi-subagents
```

## Basic usage

**No configuration needed** — use natural language directly:

```text
Use reviewer to review this diff.
Ask oracle for a second opinion on my current plan.
Use scout to understand this code, then ask me clarification questions.
Run parallel reviewers: one for correctness, one for tests, one for unnecessary complexity.
```

Precise commands (optional):

| Command | Purpose |
|---|---|
| `/run <agent> [task]` | Run a single agent |
| `/chain a "t1" -> b "t2"` | Chained execution; `(x \| y)` runs in parallel inside parens |
| `/parallel a "t1" -> b "t2"` | Parallel execution |
| `/run-chain <chainName> -- <task>` | Launch a saved `.chain.md` workflow |
| `/subagents [agent] [model\|thinking\|prompt\|details]` | Interactively view/modify an agent's model, thinking level, or prompt |
| `/subagents-models [agent]` | View the runtime model mapping |
| `/subagent-cost` | View parent/child token and cost usage |
| `/subagents-doctor` | Read-only installation diagnostics |
| `/subagents-watchdog [status\|on\|off\|model ...]` | Optional watchdog change review (a strong model like Opus 4.8 or GPT 5.5 is recommended) |

## Configuration

Written to `~/.pi/agent/settings.json` (user level) or `.pi/settings.json` (project level):

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

Key points:

- `defaultModel`/`defaultThinking` apply to built-in agents not explicitly set; `agentOverrides.<name>` is more specific and takes precedence
- Model IDs support fuzzy matching (separators, case, date suffixes all resolve)
- If a relay/third-party provider rejects model IDs with a thinking suffix, set `"disableThinking": true`
- `projectRootResolution: "git-root"` anchors the repo root in monorepos/worktrees

## Notes

- **Subagents are separate sessions; token consumption multiplies** — usage-based (relay) users should watch usage, monitorable via `/subagent-cost`
- Subagents share the permission surface with the main agent; `worker` modifies files; the watchdog is off by default and must be explicitly enabled
- Run traces and artifacts live in `~/.pi/agent/profiles/pi-subagents/` and the workspace `.pi-subagents/`

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-subagents\README.md` (1,873 lines, covering chain syntax, workflow isolation, and more)
- GitHub: https://github.com/nicobailon/pi-subagents
