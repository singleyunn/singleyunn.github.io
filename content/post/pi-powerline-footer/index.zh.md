---
title: "pi-powerline-footer 扩展使用说明"
description: "介绍 Pi 的状态栏扩展，以及如何显示模型、上下文、Git 和会话信息。"
date: "2026-08-01"
slug: "pi-powerline-footer"
categories: [AI 工具]
tags: ["Pi","扩展","终端"]
image: "/img/article-covers/pi-powerline-footer.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：0.9.0 ｜ 许可：**无 license（个人使用可，商用需谨慎）**

## 工具介绍

pi-powerline-footer 把 Pi 默认底栏替换为 Powerline 风格状态条（灵感来自 Powerlevel10k / oh-my-pi），并附带多项界面增强：

- **状态条**：模型、思考级别（`think:off/med` 等带颜色）、路径、git 分支与改动数、context 占用（>70% 黄、>90% 红预警）、tokens、成本、订阅状态
- **欢迎覆盖层**：启动时显示 logo、模型信息、快捷键、加载统计，30 秒或任意键关闭（可禁用）
- **Editor stash**：`Alt+S` 暂存编辑器内容，清空后快速提问，完成自动恢复
- **Working Vibes**：`/vibe star trek` 等主题化加载文案
- **固定编辑器模式**：聊天区滚动而编辑器固定（`/powerline fixed-editor` 控制）
- **Bash 模式**：`ctrl+shift+b` 保持托管 shell 会话，`cd` 与导出变量跨命令生效，带幽灵建议补全
- 预设：`default` / `minimal` / `compact` / `full` / `nerd` / `ascii`

来源：GitHub `nicobailon/pi-powerline-footer`（357★，活跃维护）。

> 注意：与 pi-atelier 同类（都改底栏状态条），二选一安装；本机当前安装的是本扩展。

## 安装

```bash
pi install npm:pi-powerline-footer
```

重启 Pi 生效。

## 卸载

```bash
pi remove npm:pi-powerline-footer
```

## 基本使用

自动激活，无需配置即可用。常用命令：

| 命令 | 作用 |
|---|---|
| `/powerline` | 切换开/关 |
| `/powerline default` | 切换预设（另有 minimal/compact/full/nerd/ascii） |
| `/powerline fixed-editor on\|off\|toggle` | 固定编辑器模式（默认开） |
| `/powerline placement above\|below\|toggle` | 主状态行位于编辑器上方/下方 |
| `/powerline mouse-scroll on\|off\|toggle` | 鼠标滚动与原生链接处理 |
| `/vibe <主题>` | 设置加载文案主题（star trek、pirate、zen 等） |
| `/cd <路径>` | 切换当前会话工作目录（支持 `~` 与补全） |

快捷键：`Alt+S` 编辑器暂存、`ctrl+shift+b` 切换 bash 模式、`ctrl+shift+t` 相关面板折叠（如已装 rpiv-todo）。

## 配置

写入 `~/.pi/agent/settings.json`（或项目 `.pi/settings.json`）：

```json
{
  "showLastPrompt": true,
  "powerline": {
    "preset": "default",
    "fixedEditor": true,
    "scrollAwayCard": true,
    "placement": "above",
    "welcome": true,
    "mouseScroll": true
  }
}
```

环境变量：`POWERLINE_NERD_FONTS=1` 强制 Nerd Font，`=0` 用 ASCII 回退（Windows 终端无 Nerd Font 时可设 0）。

## 注意事项

- 固定编辑器模式会接管鼠标滚动；在 tmux 等复用器内如滚动冲突，用 `/powerline fixed-editor off` 关闭
- 无 license：代码可读可用，但不具备开源许可的法律保障，商用前确认作者授权
- 若同时安装 pi-atelier，两者会争抢底栏，建议只保留一个

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-powerline-footer\README.md`
- GitHub：https://github.com/nicobailon/pi-powerline-footer
