---
title: "Claude-HUD StatusLine 配置参考"
description: "介绍 Claude-HUD 状态行插件的安装方式、布局、上下文、用量和 Git 状态配置。"
date: "2026-08-01"
slug: "claude-hud-statusline"
categories: [AI 工具]
tags: ["Claude Code","状态栏","配置"]
image: "/img/article-covers/claude-hud-statusline.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

claude-hud 是 Claude Code 的状态行插件，在终端底部显示会话信息。当前版本 0.3.0。

## 安装

在 `settings.json` 或 `settings.local.json` 中配置 `statusLine`：

```json
{
  "statusLine": {
    "type": "command",
    "command": "node C:\\Users\\<user>\\.claude\\plugins\\claude-hud\\statusline.mjs"
  }
}
```

> **Windows 注意：** 路径不能含空格。不要用 `cmd.exe /d /s /c` 包裹，直接调 `node`。详见 Claude-HUD-StatusLine配置排查。

## 配置文件

HUD 配置文件位于 `~/.claude/plugins/claude-hud/config.json`。首次使用不存在，需手动创建。

## 布局模式

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `lineLayout` | `"expanded"` | 布局模式：`expanded`（多行）或 `compact`（单行） |
| `showSeparators` | `false` | 是否在静态行与活动行之间显示分隔线 |
| `pathLevels` | `1` | 项目路径显示层级 1/2/3 |
| `maxWidth` | `null` | 最大宽度（字符数），`null` 自动检测终端宽度 |
| `forceMaxWidth` | `false` | 强制使用 `maxWidth` 而非终端检测值 |

## 元素顺序

`elementOrder` 数组控制各行的显示顺序，默认值：

```json
["project", "addedDirs", "context", "usage", "promptCache", "memory", "environment",
 "tools", "skills", "mcp", "agents", "todos", "sessionTime"]
```

### 静态行

| 元素 | 对应配置 | 说明 |
|------|----------|------|
| `project` | `display.showProject` | 项目名和 git 分支 |
| `addedDirs` | `display.showAddedDirs` | 已添加的工作目录 |
| `context` | `display.showContextBar` / `display.contextValue` | 上下文窗口使用量 + 模型名 |
| `usage` | `display.showUsage` / `display.usageValue` | API 配额用量（5h / 7d） |
| `promptCache` | `display.showPromptCache` | 提示缓存状态 |
| `memory` | `display.showMemoryUsage` | 已加载 memory 文件数 |
| `environment` | `display.environmentThreshold` | CLAUDE.md / MCP / hooks 等配置统计 |
| `sessionTime` | `display.showSessionStartDate` / `display.showLastResponseAt` | 会话时间 |

### 活动行（有活动时才显示）

| 元素 | 对应配置 | 说明 |
|------|----------|------|
| `tools` | `display.showTools` | 工具调用（默认 `false`） |
| `skills` | `display.showSkills` | 技能调用（默认 `false`） |
| `mcp` | `display.showMcp` | MCP 服务器调用（默认 `false`） |
| `agents` | `display.showAgents` | 子 agent 运行状态（默认 `false`） |
| `todos` | `display.showTodos` | 任务列表（默认 `false`） |

### 行合并

`display.mergeGroups` 将多行合并为一行（用 `│` 分隔），默认合并 `["context", "usage"]`。

## 显示选项

### 上下文

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `display.showModel` | `true` | 是否显示模型名 |
| `display.showContextBar` | `true` | 是否显示进度条 |
| `display.contextValue` | `"percent"` | 显示方式：`percent` / `tokens` / `remaining` / `both` |
| `display.contextWarningThreshold` | `70` | 警告阈值（黄色） |
| `display.contextCriticalThreshold` | `85` | 危险阈值（红色） |
| `display.showTokenBreakdown` | `true` | 超阈值时显示输入/cache token 明细 |
| `display.showOutputStyle` | `false` | 输出风格标记 |

### 用量与配额

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `display.showUsage` | `true` | 是否显示 API 用量 |
| `display.usageValue` | `"percent"` | 显示方式：`percent` / `remaining` |
| `display.usageBarEnabled` | `true` | 是否显示用量进度条 |
| `display.usageCompact` | `false` | 紧凑模式（只显示百分比） |
| `display.usageThreshold` | `0` | 用量达此百分比才显示 |
| `display.sevenDayThreshold` | `80` | 7 天用量达此值才显示 |
| `display.showResetLabel` | `true` | 显示重置倒计时 |

### 费用与性能

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `display.showCost` | `false` | ⭐ 显示 API 费用估算 |
| `display.showDuration` | `false` | ⭐ `⏱️` 会话运行时长 |
| `display.showSpeed` | `false` | ⭐ `out: X tok/s` 输出速度 |
| `display.showSessionTokens` | `false` | 会话累计 token 消耗 |

### Git

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `gitStatus.enabled` | `true` | 是否显示 git 状态 |
| `gitStatus.showDirty` | `true` | 是否显示脏状态 `*` |
| `gitStatus.showAheadBehind` | `false` | 是否显示 `↑2 ↓1` |
| `gitStatus.showFileStats` | `false` | ⭐ 是否显示 `!M +A ✘D ?U` |
| `gitStatus.branchOverflow` | `"truncate"` | 长分支名处理：`truncate` / `wrap` |
| `gitStatus.pushWarningThreshold` | `0` | 未推送提交警告阈值 |
| `gitStatus.pushCriticalThreshold` | `0` | 未推送提交危险阈值 |

### 其他

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `display.showMemoryUsage` | `false` | 加载的 memory 文件数量 |
| `display.showPromptCache` | `false` | 提示缓存命中状态 |
| `display.showCompactions` | `false` | 上下文压缩次数 |
| `display.showConfigCounts` | `false` | CLAUDE.md / MCP 等统计 |
| `display.showSessionName` | `false` | 会话名（`/rename` 设置） |
| `display.showClaudeCodeVersion` | `false` | CC 版本号 |
| `display.showEffortLevel` | `false` | 推理力度等级 |
| `display.showAdvisor` | `false` | Advisor 模型 |
| `display.modelFormat` | `"full"` | 模型显示格式：`full` / `compact` / `short` |
| `display.modelOverride` | `""` | 覆盖显示的模型名 |
| `display.showProvider` | `false` | 显示 API provider |
| `display.autocompactBuffer` | `"enabled"` | 是否启用自动压缩缓冲显示 |
| `display.customLine` | `""` | 自定义文字（最长 80 字符） |
| `display.customLinePosition` | `"last"` | 自定义文字位置：`first` / `last` |
| `display.timeFormat` | `"relative"` | 时间格式：`relative` / `absolute` / `both` / `elapsed` / `elapsedAndAbsolute` |
| `display.toolNameMaxLength` | `0` | 工具名最大长度，0 不限 |
| `display.toolsMaxVisible` | `4` | 最多显示几个工具 |
| `display.promptCacheTtlSeconds` | `300` | 缓存 TTL 秒数 |

## 颜色

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

颜色值支持：`dim` / `red` / `green` / `yellow` / `magenta` / `cyan` / `brightBlue` / `brightMagenta` / 0-255 色值 / `#RRGGBB`。

## 推荐配置

数据学习项目，信息密度优先：

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

## 相关文件

| 文件 | 说明 |
|------|------|
| `~/.claude/plugins/claude-hud/config.json` | HUD 用户配置 |
| `~/.claude/plugins/claude-hud/statusline.mjs` | 启动器脚本 |
| `.claude/settings.local.json` | 项目级 statusLine 命令 |
| `.claude/settings.json` | 用户级 statusLine 命令 |

## 日期

2026-06-24
