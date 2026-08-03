---
title: "Git 安装配置指南"
description: "面向 Windows 初学者的 Git 安装、身份配置和常用设置指南。"
date: "2026-08-01"
slug: "git-windows-install"
categories: [开发环境]
tags: ["Git","Windows","入门"]
image: "/img/article-covers/git-windows-install.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

> 安装方式：winget（Windows 包管理器）
> 更新日期：2026-06-12

## 一、前置：winget 镜像源（可选）

如果 winget 源访问慢，可替换为镜像源：

```powershell
# 查看当前源
winget source list

# 替换为 USTC 镜像（二选一）
winget source remove winget
winget source add winget https://mirrors.ustc.edu.cn/winget-source/index.json --trust-level trusted

# 恢复官方源
winget source remove winget
winget source add winget https://cdn.winget.microsoft.com/cache/source.msix
```

## 二、安装 Git

```powershell
winget install --id Git.Git -e --source winget
```

> winget 安装为静默模式，不会弹出安装向导界面，所有选项走默认值。
> 安装完成后可通过 `git config` 逐一配置个性化设置（详见第三节）。

## 三、安装后配置（git config）

### 3.1 默认编辑器

设置 Git 默认使用的文本编辑器。推荐 VS Code 或 Vim。

```powershell
# VS Code（推荐）
git config --global core.editor "code --wait"

# Vim（Git 默认）
git config --global core.editor "vim"

# Nano
git config --global core.editor "nano"

# Notepad++
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -nosession"
```

### 3.2 默认分支名

设置 `git init` 时新建仓库的默认分支名称。

```powershell
# main（推荐，GitHub 默认）
git config --global init.defaultBranch main

# master（Git 传统默认）
git config --global init.defaultBranch master
```

### 3.3 行尾符号转换

跨平台协作时统一换行符，防止 Windows 与 Linux/macOS 之间出现行尾混乱。

```powershell
# Windows 推荐：检出时转 CRLF，提交时转 LF
git config --global core.autocrlf true

# Mac/Linux 推荐：检出时不转换，提交时转 LF
git config --global core.autocrlf input

# 不转换（不推荐跨平台协作）
git config --global core.autocrlf false
```

### 3.4 HTTPS 后端传输

选择 Git 用于 HTTPS 连接的 SSL/TLS 实现。

```powershell
# Windows Secure Channel（推荐 Windows 用户，使用系统证书存储）
git config --global http.sslBackend schannel

# OpenSSL（Git 默认）
git config --global http.sslBackend openssl
```

### 3.5 SSH 执行文件

选择 Git 使用的 SSH 客户端。

```powershell
# 使用 Windows 系统自带 OpenSSH（推荐，无需额外安装）
git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"

# 使用 Git 自带的 OpenSSH（默认）
# 无需配置

# 使用自定义 SSH
git config --global core.sshCommand "C:/path/to/ssh.exe"
```

### 3.6 git pull 行为

设置 `git pull` 时默认的合并策略。

```powershell
# merge（推荐）：git pull = git fetch + git merge
git config --global pull.rebase false

# rebase：git pull = git fetch + git rebase
git config --global pull.rebase true

# fast-forward only：仅允许快进合并，拒绝非快进
git config --global pull.ff only
```

### 3.7 凭证帮助程序

Git 操作（如 push/pull）需要认证时，自动管理凭据，避免每次输入账号密码。

```powershell
# Git Credential Manager（推荐 Windows）
git config --global credential.helper manager-core

# 或新版 GCM
git config --global credential.helper manager
```

### 3.8 文件系统缓存

启用后 Git 会缓存文件系统信息（如目录内容），显著提升 Windows 上的性能。

```powershell
# 启用文件系统缓存（Windows 推荐）
git config --global core.fscache true
```

### 3.9 符号链接

```powershell
# 启用符号链接支持（需要 Windows 开发者模式）
git config --global core.symlinks true
```

### 3.10 文件系统监视器（实验性）

Git 内置文件监视器，监听文件变更，替代反复扫描目录，提升 `git status` 等命令速度。

```powershell
# 启用内置文件系统监视器
git config --global core.fsmonitor true

# 或启用实验性内置监视器
git config --global feature.enableBuiltinFSMonitor true
```

### 3.11 用户信息（必配）

```powershell
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

## 四、一键配置脚本

安装完 Git 后可直接跑以下脚本，完成 Windows 推荐配置：

```powershell
# Windows 推荐一键配置
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
git config --global core.autocrlf true
git config --global core.fscache true
git config --global http.sslBackend schannel
git config --global pull.rebase false
git config --global credential.helper manager-core
git config --global core.editor "code --wait"
```

## 五、验证安装

```powershell
git --version         # 查看版本
git config --list     # 查看所有配置
git config --global --list  # 查看全局配置
```

## 参考

- Git 官方文档：[git-scm.com](https://git-scm.com)
- Git 安装向导详解：[CSDN - Git 详细安装教程](https://blog.csdn.net/mukes/article/details/115693833)
