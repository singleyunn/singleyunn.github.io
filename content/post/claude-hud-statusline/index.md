---
title: "Claude-HUD StatusLine Configuration Reference"
description: "Claude-HUD statusline plugin: installation, layout, context, usage, and git status configuration."
date: "2026-08-01"
slug: "claude-hud-statusline"
categories: [AI 工具]
tags: ["Claude Code","Status Bar","Configuration"]
image: "/img/article-covers/claude-hud-statusline.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

claude-hud is a statusline plugin for Claude Code that shows session info at the bottom of the terminal. Current version 0.3.0.

## Installation

Configure `statusLine` in `settings.json` or `settings.local.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "node C:\\Users\\<user>\\.claude\\plugins\\claude-hud\\statusline.mjs"
  }
}
```

> **Windows note:** the path must not contain spaces. Don't wrap it in `cmd.exe /d /s /c`; call `node` directly. See the Claude-HUD StatusLine troubleshooting notes for details.

## Config file

The HUD config file is `~/.claude/plugins/claude-hud/config.json`. It doesn't exist on first use; create it manually.

## Layout modes

| Setting | Default | Description |
|--------|--------|------|
| `lineLayout` | `"expanded"` | Layout mode: `expanded` (multi-line) or `compact` (single line) |
| `showSeparators` | `false` | Show separators between static and active lines |
| `pathLevels` | `1` | Project path depth shown: 1/2/3 |
| `maxWidth` | `null` | Max width (chars); `null` auto-detects terminal width |
| `forceMaxWidth` | `false` | Force `maxWidth` instead of the detected terminal width |

## Element order

The `elementOrder` array controls the display order of each line, default:

```json
["project", "addedDirs", "context", "usage", "promptCache", "memory", "environment",
 "tools", "skills", "mcp", "agents", "todos", "sessionTime"]
```

### Static lines

| Element | Related config | Description |
|------|----------|------|
| `project` | `display.showProject` | project name and git branch |
| `addedDirs` | `display.showAddedDirs` | added working directories |
| `context` | `display.showContextBar` / `display.contextValue` | context window usage + model name |
| `usage` | `display.showUsage` / `display.usageValue` | API quota usage (5h / 7d) |
| `promptCache` | `display.showPromptCache` | prompt cache status |
| `memory` | `display.showMemoryUsage` | number of loaded memory files |
| `environment` | `display.environmentThreshold` | stats for CLAUDE.md / MCP / hooks config |
| `sessionTime` | `display.showSessionStartDate` / `display.showLastResponseAt` | session time |

### Active lines (shown only when active)

| Element | Related config | Description |
|------|----------|------|
| `tools` | `display.showTools` | tool calls (default `false`) |
| `skills` | `display.showSkills` | skill calls (default `false`) |
| `mcp` | `display.showMcp` | MCP server calls (default `false`) |
| `agents` | `display.showAgents` | subagent run status (default `false`) |
| `todos` | `display.showTodos` | task list (default `false`) |

### Line merging

`display.mergeGroups` merges multiple lines into one (separated by `│`); default merge is `["context", "usage"]`.

## Display options

### Context

| Setting | Default | Description |
|--------|--------|------|
| `display.showModel` | `true` | show the model name |
| `display.showContextBar` | `true` | show a progress bar |
| `display.contextValue` | `"percent"` | display style: `percent` / `tokens` / `remaining` / `both` |
| `display.contextWarningThreshold` | `70` | warning threshold (yellow) |
| `display.contextCriticalThreshold` | `85` | critical threshold (red) |
| `display.showTokenBreakdown` | `true` | show input/cache token details past threshold |
| `display.showOutputStyle` | `false` | output style marker |

### Usage and quota

| Setting | Default | Description |
|--------|--------|------|
| `display.showUsage` | `true` | show API usage |
| `display.usageValue` | `"percent"` | display style: `percent` / `remaining` |
| `display.usageBarEnabled` | `true` | show a usage progress bar |
| `display.usageCompact` | `false` | compact mode (percent only) |
| `display.usageThreshold` | `0` | only show usage above this percent |
| `display.sevenDayThreshold` | `80` | only show 7-day usage above this value |
| `display.showResetLabel` | `true` | show reset countdown |

### Cost and performance

| Setting | Default | Description |
|--------|--------|------|
| `display.showCost` | `false` | ⭐ show estimated API cost |
| `display.showDuration` | `false` | ⭐ `⏱️` session elapsed time |
| `display.showSpeed` | `false` | ⭐ `out: X tok/s` output speed |
| `display.showSessionTokens` | `false` | session cumulative token usage |

### Git

| Setting | Default | Description |
|--------|--------|------|
| `gitStatus.enabled` | `true` | show git status |
| `gitStatus.showDirty` | `true` | show dirty marker `*` |
| `gitStatus.showAheadBehind` | `false` | show `↑2 ↓1` |
| `gitStatus.showFileStats` | `false` | ⭐ show `!M +A ✘D ?U` |
| `gitStatus.branchOverflow` | `"truncate"` | long branch names: `truncate` / `wrap` |
| `gitStatus.pushWarningThreshold` | `0` | unpushed commit warning threshold |
| `gitStatus.pushCriticalThreshold` | `0` | unpushed commit critical threshold |

### Other

| Setting | Default | Description |
|--------|--------|------|
| `display.showMemoryUsage` | `false` | number of loaded memory files |
| `display.showPromptCache` | `false` | prompt cache hit status |
| `display.showCompactions` | `false` | context compaction count |
| `display.showConfigCounts` | `false` | CLAUDE.md / MCP stats |
| `display.showSessionName` | `false` | session name (set via `/rename`) |
| `display.showClaudeCodeVersion` | `false` | CC version |
| `display.showEffortLevel` | `false` | reasoning effort level |
| `display.showAdvisor` | `false` | Advisor model |
| `display.modelFormat` | `"full"` | model display format: `full` / `compact` / `short` |
| `display.modelOverride` | `""` | override the displayed model name |
| `display.showProvider` | `false` | show the API provider |
| `display.autocompactBuffer` | `"enabled"` | enable auto-compaction buffer display |
| `display.customLine` | `""` | custom text (max 80 chars) |
| `display.customLinePosition` | `"last"` | custom text position: `first` / `last` |
| `display.timeFormat` | `"relative"` | time format: `relative` / `absolute` / `both` / `elapsed` / `elapsedAndAbsolute` |
| `display.toolNameMaxLength` | `0` | max tool name length; 0 = unlimited |
| `display.toolsMaxVisible` | `4` | max number of tools shown |
| `display.promptCacheTtlSeconds` | `300` | cache TTL in seconds |

## Colors

```json
{
  "colors": {
    "context": "green",
    "usage": "brightBlue",
    "warning": "yellow",
    "usageWarning": "brightMagenta",
    "critical": "red",
    "model": "cyan",
    "project": "yellow",
    "git": "magenta",
    "gitBranch": "cyan",
    "label": "dim",
    "custom": 208
  }
}
```

Color values support: `dim` / `red` / `green` / `yellow` / `magenta` / `cyan` / `brightBlue` / `brightMagenta` / 0-255 values / `#RRGGBB`.

## Recommended configuration

For a data-learning project, prioritizing information density:

```json
{
  "display": {
    "showCost": true,
    "showDuration": true,
    "showSpeed": true,
    "showAgents": true
  },
  "gitStatus": {
    "showFileStats": true
  }
}
```

## Related files

| File | Description |
|------|------|
| `~/.claude/plugins/claude-hud/config.json` | HUD user config |
| `~/.claude/plugins/claude-hud/statusline.mjs` | launcher script |
| `.claude/settings.local.json` | project-level statusLine command |
| `.claude/settings.json` | user-level statusLine command |

## Date

2026-06-24
