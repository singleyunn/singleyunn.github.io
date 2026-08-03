---
title: "pi-tool-display Extension Guide"
description: "Compact rendering of Pi tool calls and visual diff of edits."
date: "2026-08-01"
slug: "pi-tool-display"
categories: [AI 工具]
tags: ["Pi","Extension","Terminal"]
image: "/img/article-covers/pi-tool-display.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 0.5.0 | License: MIT

## Introduction

pi-tool-display optimizes how Pi renders tool calls, inspired by the OpenCode style:

- **Compact rendering**: `read`, `grep`, `find`, `ls`, `bash`, `edit`, `write` are collapsed by default instead of flooding the screen
- **Diff visualization**: edit/write changes render in split/unified layouts with syntax highlighting and inline emphasis; in-flight streaming edits show a pending preview directly
- **MCP-aware rendering**: three modes — hidden / summarized / preview
- **Thinking labels**: stage labels for thinking during streaming and in final messages (with context sanitization to avoid leaking display labels back to the model)
- **Native user input box** (optional): markdown-aware rendering
- Presets: `opencode` (default) / `balanced` / `verbose`
- Coexists with other rendering extensions (per-tool ownership toggle)

Source: GitHub `MasuRii/pi-tool-display` (234★, MIT).

## Installation

```bash
pi install npm:pi-tool-display
```

## Uninstallation

```bash
pi remove npm:pi-tool-display
```

## Basic usage

Takes effect automatically after installation. Settings and commands:

| Command | Purpose |
|---|---|
| `/tool-display` | Open the interactive settings panel (presets, per-tool output modes, preview line counts, diff layout, etc.) |
| `/tool-display show` | Show a summary of the current effective config |
| `/tool-display reset` | Restore the default opencode preset |
| `/tool-display preset opencode\|balanced\|verbose` | Switch presets |

Advanced options live in the extension's `config.json` (parts not covered by the panel).

## Configuration notes

- Preview line count and bash collapse line count are adjustable directly in the `/tool-display` panel
- MCP output modes only appear when an MCP environment is detected
- If installed alongside other tool-rendering extensions, use the per-tool ownership toggle to assign which tool each extension renders, avoiding double rendering

## Notes

- Only affects the display layer; tool execution results are unchanged; closing or uninstalling restores Pi's default rendering
- Same author as pi-rtk-optimizer and complementary in direction (one manages display, the other context compression); they can be installed together

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-tool-display\README.md`
- GitHub: https://github.com/MasuRii/pi-tool-display
