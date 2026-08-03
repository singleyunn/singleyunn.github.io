---
title: "uv 开发环境搭建指南（Windows）"
description: "从 VS Code、uv、Python 到虚拟环境，逐步搭建可运行的开发环境。"
date: "2026-08-01"
slug: "uv-development-environment"
categories: [开发环境]
tags: ["uv","Python","Windows"]
image: "/img/article-covers/uv-development-environment.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

从零开始，每一步都写清楚。跟着做就能跑。

---

## 1. 安装 VS Code

**winget 方式（安装到 D 盘）：**

```powershell
winget install Microsoft.VisualStudioCode --location D:\tools\VSCode
```

装完自动加 PATH，无需重启。开始菜单搜索"Visual Studio Code"打开即可。

> `--location D:\tools\VSCode` 指定安装目录，不占 C 盘空间。目录不存在会自动建。
>
> 如果 winget 安装方式不可用，到 https://code.visualstudio.com 手动下载 MSI 安装包，安装时自定义目录选 `D:\tools\VSCode`。

---

## 2. 安装 VS Code 必备扩展

打开 VS Code，左侧点扩展图标（或 `Ctrl+Shift+X`），搜索安装以下 4 个：

| 搜索关键词 | 扩展名 | 用途 |
|-----------|--------|------|
| `Chinese` | **Chinese (Simplified) Language Pack** | 中文界面 |
| `Python` | **Python** (Microsoft) | Python 语言支持 |
| `Pylance` | **Pylance** (Microsoft) | 代码补全/类型检查 |
| `Jupyter` | **Jupyter** (Microsoft) | Notebook 支持 |

装完中文包后重启 VS Code，界面变为中文。

> 如果扩展商店连不上（搜索报错 `Failed to fetch`），点右上角 `...` → "Install from VSIX..." → 从别处获取 `.vsix` 文件离线安装。

---

## 3. 安装 uv

打开 PowerShell，执行官方安装脚本（自动下载 + 自动加 PATH）：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

> `-ExecutionPolicy ByPass` 绕过执行策略检查，否则脚本可能被系统拦截。这只在当前命令生效，不改系统配置。

装完关掉当前 PowerShell，重开新窗口验证：

```powershell
uv --version
# 输出类似: uv 0.7.x
```

> 只加 uv **命令行工具** 的 PATH。Python 解释器由 uv 自动下载管理（存在 `~/.uv/` 内部），**不需要、也不要加 PATH**，uv 自己能找到。手动加 PATH 反而可能让 `python` 命令与 uv 管理的解释器混淆。

---

## 4. 创建项目

PowerShell 默认在 `C:\Users\你的用户名`，建议创建到 D 盘或其他盘：

```powershell
# 切到 D 盘（不放 C 盘，避免以后重装系统丢项目）
D:
# 建一个 projects 文件夹（mkdir = 新建文件夹）
mkdir projects
# 进入这个文件夹（cd = 进入目录）
cd projects

# 创建项目（名字自己定）
uv init my-analysis
cd my-analysis
```

生成的文件：

```
my-analysis/
├── pyproject.toml    # 项目配置文件（核心）
└── hello.py          # 示例脚本（可删除）
```

---

## 5. pyproject.toml 配置

uv 靠 `pyproject.toml` 知道装什么包、用什么 Python 版本。以下是 Polars 学习项目的标准配置：

```toml
[project]
name = "polars-learning"
version = "0.1.0"
description = "Polars 数据处理学习项目"
requires-python = ">=3.13"
dependencies = [
    "polars[pyarrow,numpy]>=1.40",
    "matplotlib>=3.9",
    "seaborn>=0.13",
    "jupyter>=1.0",
]

[dependency-groups]
dev = [
    "pytest>=8.0",
]
```

**依赖说明：**

| 包 | 用途 |
|---|---|
| `polars[pyarrow,numpy]` | 核心库 + pyarrow 扩展（读写 Parquet/Arrow）+ numpy 集成（与 numpy 数组互转） |
| `matplotlib` | 基础可视化 |
| `seaborn` | 高级统计图表（依赖 matplotlib，无需重复指定） |
| `jupyter` | Notebook 环境，VS Code 直接用 |
| `pytest` (dev 组) | 测试框架，仅开发时使用，不打包进产品 |

> `requires-python = ">=3.13"` + `uv python pin 3.13` 固定版本。推荐用 `>=` 写下限，uv 生成 `uv.lock` 锁定精确版本，保证每次安装一致。

---

## 6. 安装 Python

```powershell
# 安装 Python 3.13（uv 自动下载，不依赖系统已有的）
uv python install 3.13

# 固定项目用这个版本（重点！不跑这步，uv sync 可能选错版本）
uv python pin 3.13

# 查看已安装的 Python
uv python list
```

> `uv python pin` 会在项目里生成 `.python-version` 文件。之后 `uv sync` / `uv venv` 都会自动用这个版本。**一定要先 pin 再 sync**，否则 uv 会选机器上满足 `requires-python` 的最高版本（比如你装了 3.14，它就用 3.14）。

---

## 7. 管理依赖

```powershell
# 添加包（会自动更新 pyproject.toml 和 uv.lock）
uv add polars seaborn

# 添加开发工具（写在 dev 组里）
uv add --dev pytest

# 删除包
uv remove seaborn

# 根据 pyproject.toml 安装所有依赖（首次运行会自动创建 .venv）
uv sync
```

> `uv sync` 是日常最常用的——改完 pyproject.toml 后跑一次即可。首次执行会自动生成 `uv.lock` 文件，锁定所有依赖的精确版本，保证每次安装一致。把这个文件提交到 Git。

---

## 8. 虚拟环境与 VS Code

`uv sync` 会自动在项目里创建 `.venv\` 文件夹。在 VS Code 中打开项目文件夹后，`Ctrl+Shift+P` → `Python: Select Interpreter` → 选 `.venv\Scripts\python.exe`。之后 VS Code 的终端、运行按钮、Jupyter 扩展都会自动用这个环境，无需手动激活。

---

## 9. 故障排查

### sync 之后 Python 版本不是我想要的

比如你装了 `uv python install 3.13`，但 `.venv\Scripts\python.exe --version` 返回 3.14。

**原因：** 没有先执行 `uv python pin 3.13`。`uv sync` 会在所有满足 `requires-python` 的版本里选最高的。

**修复：**

```powershell
# 1. 删掉版本不对的 .venv
rm -r -force .venv
rm uv.lock

# 2. 固定版本（这回别忘了）
uv python pin 3.13

# 3. 重新 sync
uv sync
```

### VS Code Jupyter 内核列表里看不到新 .venv 的 Python

`Ctrl+Shift+P` → "Python: Select Interpreter" → 手动浏览到 `.venv\Scripts\python.exe`。

如果选了还是不出现：`Ctrl+Shift+P` → "Developer: Reload Window"。

---

## 常用命令速查

```powershell
# ---- 项目 ----
uv init 项目名              # 创建新项目
uv add 包名                 # 添加依赖
uv add --dev 包名           # 添加开发依赖
uv remove 包名              # 删除依赖
uv sync                     # 根据 pyproject.toml 安装所有依赖

# ---- Python ----
uv python install 3.13      # 安装 Python
uv python list              # 查看已装 Python
uv python pin 3.13          # 固定项目 Python 版本
```
