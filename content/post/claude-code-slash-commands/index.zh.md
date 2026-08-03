---
title: "Claude Code 斜杠命令速查"
description: "整理 Claude Code 中管理对话、配置环境和日常开发最常用的斜杠命令。"
date: "2026-08-01"
slug: "claude-code-slash-commands"
categories: [命令速查]
tags: ["Claude Code","命令速查"]
image: "/img/article-covers/claude-code-slash-commands.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 在输入框敲 `/` 会自动弹出当前版本的全部命令列表，边打边筛最可靠。本表为高频命令，具体以弹出列表为准（版本不同略有差异）。
> 适用：claude-opus-4-8[1m]，本机 npm 全局安装。

## 最常用 · 管理对话

| 命令 | 作用 |
|------|------|
| `/clear` | 清空当前对话，**从零开始**（上下文全丢，换全新任务时用） |
| `/compact` | 压缩对话历史（保留摘要，**腾空间但不丢线索**，长聊变卡时用） |
| `/context` | 看上下文窗口占用（"内存仪表盘"） |
| `/cost` | 看本次会话花了多少 token / 钱 |
| `/resume` | 恢复之前中断的某次对话 |
| `/exit` `/quit` | 退出 |

> 🔑 **clear vs compact**：clear 清空全丢，compact 压缩保留摘要。怕丢线索就用 compact。

## 配置 · 环境

| 命令 | 作用 |
|------|------|
| `/model` | 切换模型（Opus / Sonnet / Haiku） |
| `/config` | 打开设置 |
| `/doctor` | 会话内体检安装状态（PATH、版本、依赖） |
| `/memory` | 编辑记忆文件（CLAUDE.md / MEMORY.md） |
| `/permissions` | 管理工具权限（哪些操作要问、哪些放行） |
| `/login` `/logout` | 登录 / 切换账号 |
| `/terminal-setup` | 配置终端集成 |

## 干活 · 功能

| 命令 | 作用 |
|------|------|
| `/init` | 扫一遍项目、自动生成 CLAUDE.md |
| `/review` | 代码审查 |
| `/mcp` | 管理 MCP 服务器 |
| `/agents` | 管理子 agent |
| `/skills` | 查看可用技能 |
| `/help` | 列出所有命令 |
| `/bug` `/feedback` | 反馈问题 |

## 实用建议

1. **不记命令就敲 `/`** —— 自动弹全部命令列表，边打边筛，比记忆靠谱。
2. **日常真正高频就三个**：`/clear`（换任务清场）、`/compact`（聊太长压缩）、`/exit`。
3. **`! 前缀` 跑 shell** —— 输入框里 `! <命令>` 可直接在会话内执行 PowerShell 命令，输出进对话（适合 `gcloud auth login` 这类需自己跑的交互命令）。

---

## 附：读懂 `/context`

| 行 | 含义 |
|------|------|
| `74.1k/1m tokens (7%)` | 当前用量 / 窗口上限（占满格百分比） |
| System prompt/tools/Skills | 系统固定开销，动不了 |
| Memory files | 加载的 CLAUDE.md + MEMORY.md |
| Messages | 对话本身，通常是大头 |
| Free space | 剩余可用，越大越宽裕 |
| Suggestions | 省 token 建议（给 AI 看的优化提示，对用户无操作要求） |

空间快满（剩余低于 ~15%）再考虑 `/compact`；远未满则无需处理。

---

更新日期：2026-06-29
