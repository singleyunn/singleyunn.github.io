---
title: "pi-mcp-adapter 扩展使用说明"
description: "用一个代理工具按需搜索和调用 MCP 服务，减少大量工具定义对上下文窗口的占用。"
date: "2026-08-01"
slug: "pi-mcp-adapter"
categories: [AI 工具]
tags: ["Pi","MCP","扩展"]
image: "/img/article-covers/pi-mcp-adapter.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 更新日期：2026-08-01 ｜ 参考版本：2.16.0 ｜ 许可：MIT

## 工具介绍

pi-mcp-adapter 解决 MCP 工具定义太占 context 的问题：一个 MCP 服务器的工具定义可能烧掉 1 万+ tokens，连几个服务器就把 context 耗掉一半。

它用**一个代理工具 `mcp()`（约 200 tokens）替代几百个工具定义**：

- 服务器**惰性启动**——不调用就不连接，需要时才启动
- 工具元数据本地缓存，搜索和描述无需实时连接
- 自动读取标准 MCP 配置文件，已有 `.mcp.json` 即可直接用
- 可导入 Cursor、Claude Code、Codex 等宿主配置（`/mcp setup`）

来源：GitHub `nicobailon/pi-mcp-adapter`（1,111★，活跃维护），官方包目录 pi.dev 下载量约 210K/月。

## 安装

```bash
pi install npm:pi-mcp-adapter
```

**安装后必须重启 Pi**（官方明确要求）。

## 卸载

```bash
pi remove npm:pi-mcp-adapter
```

## 基本使用

无需额外配置。模型会自动调用 `mcp()` 工具：

```
mcp({ search: "screenshot" })          # 按关键词搜索可用工具
mcp({ tool: "chrome_devtools_take_screenshot", args: { format: "png" } })
```

常用命令：

| 命令 | 作用 |
|---|---|
| `/mcp` | 打开 MCP 管理界面（首次会提示检测到的配置文件） |
| `/mcp setup` | 导入宿主配置（Cursor/Claude Code/Codex）或脚手架 `.mcp.json` |
| `/mcp disable <server>` / `/mcp enable <server>` | 启用/禁用某个服务器（只写 `.pi/mcp.json`，不改源文件） |

命令行工具：`pi-mcp-adapter init` 扫描宿主配置并补全兼容导入。

## 配置

配置文件优先级（高→低）：

1. `~/.config/mcp/mcp.json`（用户全局共享）
2. `~/.agents/mcp.json` 与 `~/.agents/mcp/mcp.json`
3. `<Pi agent 目录>/mcp.json`（即 `~/.pi/agent/mcp.json`，Pi 全局覆盖）
4. `.mcp.json`（项目共享）
5. `.pi/mcp.json`（Pi 项目覆盖）

示例 `.mcp.json`：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

## 注意事项

- OAuth 凭据存入操作系统凭据库，按服务器名绑定 URL；不可用时**失败关闭**，不会回退明文存储
- 宿主配置默认**不自动加载**（`settings.hostConfigDiscovery` 默认 `"off"`），需 `/mcp setup` 显式导入
- 禁用/启用标记持久化在 `.pi/mcp.json`，改后执行 `/reload` 刷新工具列表

## 参考

- 本地已装包 README：`%USERPROFILE%\.pi\agent\npm\node_modules\pi-mcp-adapter\README.md`
- GitHub：https://github.com/nicobailon/pi-mcp-adapter
