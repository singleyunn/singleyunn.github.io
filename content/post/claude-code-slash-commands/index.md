---
title: "Claude Code Slash Commands Cheatsheet"
description: "The most useful slash commands in Claude Code for managing conversations, configuring the environment, and daily development."
date: "2026-08-01"
slug: "claude-code-slash-commands"
categories: [命令速查]
tags: ["Claude Code","Command Reference"]
image: "/img/article-covers/claude-code-slash-commands.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Typing `/` in the input box shows the full command list for your current version, with live filtering as you type — the most reliable way to explore. This table covers high-frequency commands; exact availability varies by version.
> Tested with: claude-opus-4-8[1m], installed globally via npm.

## Most used · conversation management

| Command | Purpose |
|------|------|
| `/clear` | Clear the current conversation, **start from scratch** (all context is lost; use it when switching to a completely new task) |
| `/compact` | Compact the conversation history (keeps a summary — **frees space without losing threads**; use when long chats get sluggish) |
| `/context` | Inspect context window usage (a "memory dashboard") |
| `/cost` | Check how many tokens / how much money this session has spent |
| `/resume` | Resume a previously interrupted conversation |
| `/exit` `/quit` | Exit |

> 🔑 **clear vs compact**: `/clear` wipes everything, `/compact` keeps a summary. Use `/compact` when you are worried about losing threads.

## Configuration · environment

| Command | Purpose |
|------|------|
| `/model` | Switch models (Opus / Sonnet / Haiku) |
| `/config` | Open settings |
| `/doctor` | In-session health check of the installation (PATH, version, dependencies) |
| `/memory` | Edit memory files (CLAUDE.md / MEMORY.md) |
| `/permissions` | Manage tool permissions (which operations ask, which are allowed) |
| `/login` `/logout` | Sign in / switch accounts |
| `/terminal-setup` | Configure terminal integration |

## Work · features

| Command | Purpose |
|------|------|
| `/init` | Scan the project and auto-generate CLAUDE.md |
| `/review` | Code review |
| `/mcp` | Manage MCP servers |
| `/agents` | Manage subagents |
| `/skills` | List available skills |
| `/help` | List all commands |
| `/bug` `/feedback` | Report issues |

## Practical tips

1. **Don't memorize commands, press `/`** — the full command list pops up automatically with live filtering; more reliable than memory.
2. **In daily work only three really matter**: `/clear` (reset for a new task), `/compact` (compress when the chat gets too long), and `/exit`.
3. **`!` prefix runs shell commands** — typing `! <command>` in the input box executes a PowerShell command in-session and streams output into the conversation (handy for interactive commands you need to run yourself, like `gcloud auth login`).

---

## Appendix: reading `/context`

| Line | Meaning |
|------|------|
| `74.1k/1m tokens (7%)` | Current usage / window limit (bar percentage) |
| System prompt/tools/Skills | Fixed system overhead, cannot be changed |
| Memory files | Loaded CLAUDE.md + MEMORY.md |
| Messages | The conversation itself, usually the largest part |
| Free space | Remaining headroom — the more, the better |
| Suggestions | Token-saving tips (optimization hints for the AI; no action required from you) |

Consider `/compact` only when space is nearly full (below ~15% remaining); otherwise there is nothing to do.

---

Updated: 2026-06-29
