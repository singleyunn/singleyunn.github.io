---
title: "pi-rtk-optimizer Extension Guide"
description: "Use rtk to optimize command output and reduce how much terminal tool results consume in context."
date: "2026-08-01"
slug: "pi-rtk-optimizer"
categories: [AI 工具]
tags: ["Pi","Extension","Context Optimization"]
image: "/img/article-covers/pi-rtk-optimizer.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 0.9.0 | License: MIT

## Introduction

pi-rtk-optimizer reduces context consumption through two paths (which saves money, especially for users of usage-based relay services):

1. **Command rewriting**: automatically rewrites `bash` tool commands into equivalent `rtk` commands (the decision is delegated to the installed `rtk rewrite`; Pi only applies Windows safety fixes); when the `rtk` binary is missing, commands run as-is and probing stops
2. **Output compression pipeline**: multi-stage compression of tool output (`bash`/`read`/`grep`):
   - ANSI stripping → test output aggregation (pass/fail counts) → build output keeps only errors/warnings → git output compression → lint aggregation → grep/rg grouped by file → source comment/whitespace filtering (none/minimal/aggressive) → smart truncation (reads under 80 lines stay exact) → **anchor safety** (hashline-anchored lines keep complete edit anchors) → final hard truncation

Source: GitHub `MasuRii/pi-rtk-optimizer` (215★, MIT), pi.dev ~11K downloads/month.

## Installation

```bash
pi install npm:pi-rtk-optimizer
```

## Uninstallation

```bash
pi remove npm:pi-rtk-optimizer
```

## Basic usage

Takes effect automatically after installation (rewriting only activates when `rtk` is present in the environment; otherwise commands run as-is). Commands:

| Command | Purpose |
|---|---|
| `/rtk` | Open the tabbed settings panel (←/→ switch pages, Enter/Space change values, Esc closes; takes effect live) |
| `/rtk show` | Show the current config and runtime status |
| `/rtk path` | Show the config file path |
| `/rtk verify` | Check whether the `rtk` binary is available |
| `/rtk stats` | View compression savings statistics per tool output type |

## Configuration notes

- Command rewriting supports "auto-rewrite" and "suggest-only" modes, toggled in the `/rtk` panel
- Source filter levels: `none` (no filtering) / `minimal` / `aggressive` (aggressively strips comments and whitespace while preserving userscript metadata)
- Anchor safety is a default protection: when hashline-anchored `read` output is detected, filtering/truncation keeps the full edit anchors intact, preventing breakage of subsequent edits

## Notes

- `rtk` must be installed separately (Rust Token Killer, `rtk-ai/rtk`, Apache 2.0); without it the extension degrades to pure output compression without errors
  - Global Windows install: `winget install rtk-ai.rtk` (beware of a similarly named imposter "Rust Type Kit" — you must use the `rtk-ai.rtk` ID)
  - Verify authenticity: `rtk gain` printing token-saving stats means it's genuine
  - After installing, run `/reload` so the extension re-detects the binary
  - Reference state (2026-08-01): rtk 0.44.0 installed; both `rtk --version` and `rtk gain` work
- Compression only affects the text entering context, not actual tool execution
- Same author as pi-tool-display; they can be installed together; extreme compression may lose small details — adjust the aggressive level as needed

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-rtk-optimizer\README.md`
- GitHub: https://github.com/MasuRii/pi-rtk-optimizer
