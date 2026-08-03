---
title: "Claude Code Permission Modes Explained"
description: "A plain-language comparison of Ask, Plan, Auto, and Bypass permission modes: risks and use cases."
date: "2026-08-01"
slug: "claude-code-permission-modes"
categories: [AI 工具]
tags: ["Claude Code","Permissions","Security"]
image: "/img/article-covers/claude-code-permission-modes.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

## Five modes compared

| Mode | Modify files | Run commands | Delete files | Network requests | Use cases |
|------|----------|----------|----------|----------|----------|
| **Ask before edits** (default) | confirm | confirm | confirm | confirm | don't trust the AI, review every step |
| **Edit automatically** | auto-allow | confirm | confirm | confirm | trust code edits, don't trust commands |
| **Plan mode** | blocked | blocked | blocked | read-only allowed | design the plan first, then act |
| **Auto mode** | auto-judge | auto-judge | auto-judge | auto-judge | daily development; the low-friction recommendation |
| **Bypass permissions** | allow all | allow all | allow all | allow all | large amounts of automation |

## Recommended strategy

```
Daily work:       Auto mode  (smart judgment; simple ops don't interrupt, high-risk ones confirm)
Unsure how to edit: Plan mode  (browse/analyze first, switch modes after the plan is approved)
Large batch ops:  Bypass permissions  (most efficient; switch back to Auto after)
Learning/auditing: Ask before edits  (review every change; good for learning code)
```

## Effort reasoning depth

Controls how deeply Claude thinks and verifies:

| Level | Speed | Use cases |
|------|------|----------|
| Fast | fastest | simple Q&A, formatting, comments |
| Default | medium | daily coding |
| Max | slowest but most careful | complex logic, data analysis, debugging stubborn bugs |

## Switching

- In VSCode, click the current mode name in the bottom-right corner → pick from the list
- The Permission mode dropdown at the bottom-left of the dialog also switches modes
