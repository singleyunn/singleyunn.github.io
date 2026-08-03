---
title: "pi-mcp-adapter Extension Guide"
description: "Search and call MCP services on demand through a single proxy tool, reducing the context window overhead of many tool definitions."
date: "2026-08-01"
slug: "pi-mcp-adapter"
categories: [AI 工具]
tags: ["Pi","MCP","Extension"]
image: "/img/article-covers/pi-mcp-adapter.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 2.16.0 | License: MIT

## Introduction

pi-mcp-adapter solves the problem of MCP tool definitions consuming too much context: a single MCP server's tool definitions can burn 10k+ tokens, and connecting a few servers eats half your context window.

It replaces hundreds of tool definitions with **one proxy tool `mcp()` (~200 tokens)**:

- Servers start **lazily** — no connection until called, started only when needed
- Tool metadata is cached locally; search and descriptions don't require a live connection
- Reads standard MCP config files automatically; an existing `.mcp.json` works out of the box
- Can import host configurations from Cursor, Claude Code, Codex, etc. (`/mcp setup`)

Source: GitHub `nicobailon/pi-mcp-adapter` (1,111★, actively maintained), official package registry pi.dev ~210K downloads/month.

## Installation

```bash
pi install npm:pi-mcp-adapter
```

**Pi must be restarted after installation** (explicitly required by the official docs).

## Uninstallation

```bash
pi remove npm:pi-mcp-adapter
```

## Basic usage

No extra configuration needed. The model calls the `mcp()` tool automatically:

```
mcp({ search: "screenshot" })          # search available tools by keyword
mcp({ tool: "chrome_devtools_take_screenshot", args: { format: "png" } })
```

Common commands:

| Command | Purpose |
|---|---|
| `/mcp` | Open the MCP management UI (prompts about detected config files on first use) |
| `/mcp setup` | Import host configs (Cursor/Claude Code/Codex) or scaffold `.mcp.json` |
| `/mcp disable <server>` / `/mcp enable <server>` | Enable/disable a server (only writes `.pi/mcp.json`, never touches source files) |

CLI tool: `pi-mcp-adapter init` scans host configs and completes compatible imports.

## Configuration

Config file precedence (high → low):

1. `~/.config/mcp/mcp.json` (user-global shared)
2. `~/.agents/mcp.json` and `~/.agents/mcp/mcp.json`
3. `<Pi agent directory>/mcp.json` (i.e. `~/.pi/agent/mcp.json`, Pi-global override)
4. `.mcp.json` (project shared)
5. `.pi/mcp.json` (Pi project override)

Example `.mcp.json`:

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

## Notes

- OAuth credentials go into the OS credential store, bound to a URL per server name; when unavailable it **fails closed** and never falls back to plaintext storage
- Host configs are **not auto-loaded** by default (`settings.hostConfigDiscovery` defaults to `"off"`); import explicitly with `/mcp setup`
- Enable/disable markers persist in `.pi/mcp.json`; run `/reload` after changes to refresh the tool list

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-mcp-adapter\README.md`
- GitHub: https://github.com/nicobailon/pi-mcp-adapter
