---
title: "Claude Code PowerShell Startup Troubleshooting"
description: "Fix the 'claude' command not being recognized on Windows, paths containing spaces, and broken npm launch entries."
date: "2026-08-01"
slug: "claude-code-powershell"
categories: [安全排障]
tags: ["Claude Code","PowerShell","Windows","Troubleshooting"]
image: "/img/article-covers/claude-code-powershell.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Problem: the `claude` command in PowerShell reports "not recognized" or the path is split on spaces.

## Root causes

### 1. Installation path contains spaces

`D:\claude code\` contains a space, so PowerShell's `&` call operator splits the path at the space, and startup fails.

**Fix**: rename it to a space-free path, e.g. `D:\tools\claude-code\`

### 2. Binary package removed by an npm auto-update

The `@anthropic-ai/claude-code-win32-x64` package is sometimes flagged as redundant and cleaned up during npm updates, deleting `claude.exe`.

**Fix**: `npm install @anthropic-ai/claude-code-win32-x64`

### 3. Global command registration no longer resolves

After renaming the install directory, the globally registered `claude` command may not resolve in new terminal sessions.

**Fix**:
- Option A: `npm install -g @anthropic-ai/claude-code@latest` to re-register
- Option B: use the project-level launcher `claude.cmd`

## Self-healing mechanism

`D:\tools\claude-code\claude.ps1` has built-in self-healing: before startup it checks whether `claude.exe` exists, and if missing, automatically runs `npm install` to reinstall — transparent to the user.

## Recommended layout

```
D:\tools\claude-code\          ← space-free install directory
  ├── claude.ps1        ← startup script (with self-healing logic)
  └── node_modules/
D:\your-project\                 ← project directory
  └── claude.cmd        ← cross-shell launch entry
```

`claude.cmd` takes precedence over the global npm command, ensuring `claude` always works in `D:\your-project\`.

## Quick verification

```powershell
# Verify the installation
& "D:\tools\claude-code\claude.ps1" --version

# Verify the project launcher
& "D:\your-project\claude.cmd" --version
```
