---
title: "rpiv-todo Extension Guide"
description: "Maintain a cross-session persistent task panel above the Pi editor."
date: "2026-08-01"
slug: "rpiv-todo"
categories: [AI 工具]
tags: ["Pi","Extension","Task Management"]
image: "/img/article-covers/rpiv-todo.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 2.3.0 | License: MIT

## Introduction

rpiv-todo gives Pi a visible task list: a `todo` tool + `/todos` command + a **live panel above the editor** (a "Todos (done/total)" title, status icons, in-progress task labels). During long tasks (research → design → implementation) you always know what the agent is doing, what's done, and what's queued.

- The list rebuilds from the session itself; **it survives `/reload` and context compaction** (no disk writes)
- Completed items collapse at the end of the current round; the panel disappears automatically when the list is empty
- Auto-collapses when exceeding the line budget, so it doesn't flood the terminal
- From the `juicesharp/rpiv-mono` family (547★, actively maintained); installable standalone; pi.dev ~29K downloads/month

## Installation

```bash
pi install npm:@juicesharp/rpiv-todo
```

**Restart the Pi session to take effect.**

## Uninstallation

```bash
pi remove npm:@juicesharp/rpiv-todo
```

## Basic usage

After restart, verify first:

```text
/todos
```

A new session shows "No todos yet". Then give a multi-step task, e.g. "add a repository layer with tests, tracked via todo". After the model calls `todo`, the panel appears above the input box and updates as progress happens.

- `ctrl+shift+t`: collapse the panel to title + one-line hint; press again to expand
- `/todos`: print the full list grouped by status at any time

## Coexistence with pi-powerline-footer

**Static analysis verified: no conflicts, can be installed together**:

- rpiv-todo registers via the standard widget API into the `aboveEditor` component container (`todo-overlay.ts` line 77, `{ placement: "aboveEditor" }`)
- powerline's fixed-editor mode **preserves that container** and includes it in its own composited layout (`index.ts` line 2432 positions the container; line 2484 renders via `compositor.renderHidden(fixedWidgetContainerAbove)`), rather than replacing it
- Keybinding conflicts are handled by powerline's built-in detection and auto-replacement mechanism

After restart, if you visually notice squeezing or overlap, try collapsing the panel with `ctrl+shift+t` or toggle `/powerline fixed-editor off` to compare.

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\@juicesharp\rpiv-todo\README.md`
- GitHub: https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-todo
