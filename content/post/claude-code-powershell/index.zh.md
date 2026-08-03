---
title: "Claude Code PowerShell 启动排障"
description: "排查 Windows 上 Claude Code 命令无法识别、路径含空格和 npm 启动入口失效。"
date: "2026-08-01"
slug: "claude-code-powershell"
categories: [安全排障]
tags: ["Claude Code","PowerShell","Windows","排障"]
image: "/img/article-covers/claude-code-powershell.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 问题：PowerShell 中 `claude` 命令报错 "not recognized" 或路径分词错误

## 根因

### 1. 安装路径含空格

`D:\claude code\` 含空格，PowerShell `&` 调用符会按空格分词路径，导致启动失败。

**修复**：重命名为无空格路径，如 `D:\tools\claude-code\`

### 2. 二进制包被 npm 自动更新清理

`@anthropic-ai/claude-code-win32-x64` 包在某些 npm 更新中被误判为冗余包清理掉，`claude.exe` 文件消失。

**修复**：`npm install @anthropic-ai/claude-code-win32-x64`

### 3. 全局命令注册失效

重命名安装目录后，npm 全局注册的 `claude` 命令在新终端会话中可能无法解析。

**修复**：
- 方案 A：`npm install -g @anthropic-ai/claude-code@latest` 重新注册
- 方案 B：使用项目目录下的启动入口 `claude.cmd`

## 自愈机制

`D:\tools\claude-code\claude.ps1` 已内置自愈逻辑：启动前检测 `claude.exe` 是否存在，缺失则自动 `npm install` 重装，用户无感知。

## 推荐架构

```
D:\tools\claude-code\          ← 无空格安装目录
  ├── claude.ps1        ← 启动脚本（含自愈逻辑）
  └── node_modules/
D:\your-project\                 ← 项目目录
  └── claude.cmd        ← 跨 shell 启动入口
```

`claude.cmd` 优先于全局 npm 命令解析，确保在 `D:\your-project\` 目录下敲 `claude` 始终可用。

## 快速验证

```powershell
# 验证安装
& "D:\tools\claude-code\claude.ps1" --version

# 验证项目启动器
& "D:\your-project\claude.cmd" --version
```
