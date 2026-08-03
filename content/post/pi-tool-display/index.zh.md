---
title: "pi-tool-display 扩展使用说明"
description: "介绍 Pi 工具调用的紧凑显示和编辑差异可视化功能。"
date: "2026-08-01"
slug: "pi-tool-display"
categories: [AI 工具]
tags: ["Pi","扩展","终端"]
image: "/img/article-covers/pi-tool-display.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：0.5.0 ｜ 许可：MIT

## 工具介绍

pi-tool-display 优化 Pi 的工具调用渲染，参考 OpenCode 风格：

- **紧凑渲染**：`read`、`grep`、`find`、`ls`、`bash`、`edit`、`write` 默认折叠显示，不刷屏
- **Diff 可视化**：edit/write 的变更以 split/unified 布局 + 语法高亮 + 行内强调展示；流式进行中的编辑直接显示 pending 预览
- **MCP 感知渲染**：隐藏/摘要/预览三种模式
- **Thinking 标签**：流式与最终消息中的思考阶段标签（并做上下文净化，避免把展示标签泄漏回模型）
- **原生用户输入框**（可选）：markdown 感知渲染
- 预设：`opencode`（默认）/ `balanced` / `verbose`
- 可与其他渲染扩展共存（per-tool 所有权开关）

来源：GitHub `MasuRii/pi-tool-display`（234★，MIT）。

## 安装

```bash
pi install npm:pi-tool-display
```

## 卸载

```bash
pi remove npm:pi-tool-display
```

## 基本使用

安装后自动生效。设置与命令：

| 命令 | 作用 |
|---|---|
| `/tool-display` | 打开交互设置面板（预设、各工具输出模式、预览行数、diff 布局等） |
| `/tool-display show` | 显示当前生效配置摘要 |
| `/tool-display reset` | 恢复默认 opencode 预设 |
| `/tool-display preset opencode\|balanced\|verbose` | 切换预设 |

高级选项在扩展的 `config.json` 中（面板未覆盖的部分）。

## 配置要点

- 预览行数与 bash 折叠行数可在 `/tool-display` 面板直接调
- MCP 输出模式仅在检测到 MCP 环境时出现
- 若与其它工具渲染扩展同装，用 per-tool 所有权开关指定哪些工具归谁渲染，避免重复渲染

## 注意事项

- 只影响显示层，不改变工具执行结果；关闭或卸载后恢复 Pi 默认渲染
- 与 pi-rtk-optimizer 同作者、方向互补（一个管显示、一个管 context 压缩），可同装

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-tool-display\README.md`
- GitHub：https://github.com/MasuRii/pi-tool-display
