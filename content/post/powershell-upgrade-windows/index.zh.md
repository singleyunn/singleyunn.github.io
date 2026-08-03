---
title: "PowerShell 升级安装指南"
description: "介绍 Windows PowerShell 7 的安装、升级、验证和常见差异。"
date: "2026-08-01"
slug: "powershell-upgrade-windows"
categories: [开发环境]
tags: ["PowerShell","Windows","命令行"]
image: "/img/article-covers/powershell-upgrade-windows.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 适用版本：PowerShell 7.x → 最新版（当前 7.6.3，LTS Long Term Support）
> 环境：Windows 11 + PowerShell 7.x + winget
> 信息源：[GitHub Release](https://github.com/PowerShell/PowerShell/releases/tag/v7.6.3) | [Microsoft Learn](https://learn.microsoft.com/en-us/powershell/scripting/whats-new/powershell-7-6)

---

## PowerShell 7.6 新功能与改进

### 亮点功能

| 功能 | 说明 |
|---|---|
| **ConsoleHost 重写** | 全新控制台宿主：24 位真彩色支持、更顺滑的命令编辑、现代化的"按任意键继续"体验 |
| **Get-Command 补全增强** | 模块未加载时的命令补全更智能，能提示模块名 |
| **Get-Error 升级** | `$ErrorView = 'DetailedView'` 展示更清晰的异常详情，堆栈可读性提升 |
| **DSC v3 类资源** | PowerShell 类现在可用作 Desired State Configuration 资源 |
| **TabExpansion2 改进** | Tab 补全对 `-Parameter` 形式参数支持更好 |

### 安全性改进

| 改进 | 说明 |
|---|---|
| **SecureWarmUp 默认启用** | 启动时主动加载并校验核心程序集签名 |
| **AMSI 集成增强** | 反恶意软件扫描接口覆盖更多脚本执行路径 |
| **JEA 修复** | Just Enough Administration 会话配置的安全修补 |

### 性能与可靠性

| 改项 | 说明 |
|---|---|
| **启动加速** | 冷启动时间缩短 5%–15%（依赖系统） |
| **Linux 剪贴板跨平台** | `Get-Clipboard` / `Set-Clipboard` 在 Linux / macOS 更稳定 |
| **COM 互操作改进** | Windows 上的 COM 对象释放更干净，减少内存泄漏 |

---

## 安装 / 升级

### 第 1 步：直接安装/升级

```powershell
winget install --id Microsoft.PowerShell
```

走微软官方源，无需任何镜像。首次安装会弹用户协议确认，按 `Y` 即可。

如果网络慢怕超时中断，加 `--accept-*` 跳过所有交互确认：

```powershell
winget install --id Microsoft.PowerShell --accept-source-agreements --accept-package-agreements
```

### 第 2 步：验证

```powershell
$PSVersionTable.PSVersion
```

输出版本号 ≥ 旧版本即成功。

如果提示 `pwsh` 找不到命令，**关闭当前窗口重新打开**（安装程序更新了 PATH，当前会话看不到）。

---

## 常见问题

### Q：网络慢、下载超时？

winget 默认走微软海外 CDN，国内偶尔不稳。两个应对方案：

**方案 A**：加接受参数跳过交互，减少超时概率

```powershell
winget install --id Microsoft.PowerShell --accept-source-agreements --accept-package-agreements
```

**方案 B**：换用中科大镜像源（需 winget ≥ 1.8）

```powershell
winget source add --name ustc-mirror --arg "https://mirrors.ustc.edu.cn/winget-source" --type Microsoft.Rest
winget install --id Microsoft.PowerShell --source ustc-mirror
```

用完镜像后记得还原，避免影响其他 winget 软件的版本判断：

```powershell
winget source remove --name ustc-mirror
winget source reset --force
```

常用镜像站：

| 名称 | URL |
|---|---|
| 中科大 | `https://mirrors.ustc.edu.cn/winget-source` |
| 上海交大 | `https://mirrors.sjtu.edu.cn/winget` |

⚠️ 注意：部分镜像站的 REST schema 与微软官方不兼容，可能报 `Failed to open the added source`。此时删源回退官方即可。

### Q：参数写错了报错？

常见误写对照：

| ❌ 错误 | ✅ 正确 | 说明 |
|---|---|---|
| `-name` | `--name` | 必须双横线 |
| `-arg` | `--arg` | 必须双横线 |
| `Microsoft. Rest` | `Microsoft.Rest` | 末尾不能有空格 |
| `vinget-source` | `winget-source` | 字母 i 不能漏 |
| `https:/mirrors...` | `https://mirrors...` | // 必须有 |

不确定可跑 `winget source add --help` 查帮助。

### Q：装了但 `pwsh` 命令不生效？

安装后 PATH 已更新，但当前会话不刷新。**关闭所有 pwsh 窗口，重新打开**即可。

---

## 速查卡

```
═══ 标准安装（推荐） ═══
  winget install --id Microsoft.PowerShell

═══ 跳过确认（网络慢时用） ═══
  winget install --id Microsoft.PowerShell --accept-source-agreements --accept-package-agreements

═══ 镜像安装（官方源太慢时用） ═══
  winget source add --name ustc-mirror --arg "https://mirrors.ustc.edu.cn/winget-source" --type Microsoft.Rest
  winget install --id Microsoft.PowerShell --source ustc-mirror

═══ 还原镜像 ═══
  winget source remove --name ustc-mirror
  winget source reset --force

═══ 验证 ═══
  $PSVersionTable.PSVersion
```

---

*最后更新：2026-07-03*
