---
title: "uv 日常使用说明"
description: "整理已有项目的 uv sync、依赖管理、Python 版本和虚拟环境维护。"
date: "2026-08-01"
slug: "uv-daily-workflow"
categories: [开发环境]
tags: ["uv","Python","命令速查"]
image: "/img/article-covers/uv-daily-workflow.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

本文讲**每天打开电脑干活时**怎么用 uv。安装步骤见《uv-开发环境搭建指南》，全新项目流程见《uv-新开项目全流程》。

---

## 1. 打开已有项目

每天打开项目只需要做一件事：进入项目目录。

```powershell
D:
cd projects\my-analysis
```

不需要手动激活虚拟环境，uv 通过项目里的 `.python-version` 和 `.venv` 自动找到正确环境。

> 如果你习惯用 VS Code，直接 `code .` 打开文件夹即可，VS Code 会自动识别 `.venv`。

---

## 2. 什么时候需要跑 `uv sync`

以下情况跑一次：

| 场景 | 原因 |
|------|------|
| 拉取了别人提交的代码（`git pull`） | `pyproject.toml` 可能多了新依赖 |
| 你自己改了 `pyproject.toml`（手动加了新包） | 需要安装新加的包 |
| `.venv` 被删了或损坏 | 重新安装所有依赖 |
| 换了台电脑 / 克隆了仓库 | 从零搭建环境 |

```powershell
uv sync
```

`uv sync` 会对比 `pyproject.toml` 和 `uv.lock`，安装缺少的包、更新锁文件。

> **不需要每次开机都跑**——依赖没变就不需要重装。

---

## 3. 添加 / 删除包

### 添加依赖

```powershell
# 添加最新版
uv add polars

# 指定版本
uv add "polars>=1.40"

# 添加 extra（比如 pyarrow 扩展）
uv add "polars[pyarrow,numpy]"

# 加到 dev 组（测试工具、格式化工具等）
uv add --dev pytest ruff

# 加到自定义组
uv add --group lint ruff
```

`uv add` 会自动：
1. 更新 `pyproject.toml`
2. 更新 `uv.lock`
3. 把包装进 `.venv`

### 删除包

```powershell
uv remove polars
```

同样自动更新三个地方。

### 查看已安装的包

```powershell
# 看项目的依赖树
uv tree

# 看 pyproject.toml 里直接声明的依赖
uv sync --dry-run
```

---

## 4. uv.lock 怎么处理

`uv.lock` 记录所有依赖的**精确版本号**（包括间接依赖）。

**必须提交到 Git。** 原因：团队里每个人、每台机器安装出来的版本完全一致，避免"我电脑上能跑"的问题。

> 不要手动编辑 `uv.lock`，让 uv 自己管理。

---

## 5. Python 版本管理

uv 把 Python 解释器统一存在 `~/.uv/` 下，**所有项目共享**。`uv python install` 是全局一次性的，新项目不需要重复安装；每个项目用 `uv python pin` 声明自己用哪个版本。

### 查看已安装的 Python

```powershell
uv python list
# 输出示例：
# cpython-3.13.0-windows-x86_64   .venv\Scripts\python.exe
# cpython-3.13.0-windows-x86_64   C:\Users\you\.uv\python\cpython-3.13.0-...\python.exe
```

### 固定项目 Python 版本

```powershell
uv python pin 3.13
```

生成 `.python-version` 文件。之后 `uv sync` / `uv venv` 都用这个版本。

### 切换版本

```powershell
# 1. 安装新版本
uv python install 3.14

# 2. 改 pin
uv python pin 3.14

# 3. 重新 sync（会重建 .venv）
uv sync
```

---

## 6. 直接运行脚本：uv run

`uv run` 可以在**不激活虚拟环境**的情况下运行命令，自动使用项目的 Python 和依赖。

```powershell
# 运行 Python 脚本
uv run python script.py

# 运行 Jupyter notebook
uv run jupyter lab

# 运行测试
uv run pytest
```

适合一次性命令，不用管环境激活。

> 每次启动 Notebook 还是建议用 VS Code 的 Jupyter 扩展（打开 `.ipynb` 直接点运行），`uv run jupyter lab` 是备选方案。

---

## 7. 虚拟环境日常维护

### 位置

`.venv/` 在项目根目录，VS Code 自动识别。

### 手动激活（很少需要）

```powershell
# PowerShell
.venv\Scripts\Activate.ps1
```

> 如果报错`无法加载文件，因为在此系统上禁止运行脚本**，运行一次**：`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`，然后重开 PowerShell。这只影响当前用户，永久生效，不是改系统策略。

### 删除重建

```powershell
rm -r -force .venv
uv sync
```

###  Python 包缓存

uv 会把下载的包缓存到 `~/.uv/`，多个项目共享，不占每个项目的空间。**不需要清理**，满了 uv 自己会处理。

---

## 8. 常见场景速查

| 你想做什么 | 命令 |
|-----------|------|
| 打开项目 | `cd 项目目录`，然后 `code .` |
| 拉取代码后安装依赖 | `uv sync` |
| 加新包 | `uv add 包名` |
| 删包 | `uv remove 包名` |
| 运行脚本 | `uv run python script.py` |
| 启动 Notebook | `uv run jupyter lab` |
| 跑测试 | `uv run pytest` |
| 看依赖树 | `uv tree` |
| 固定 Python 版本 | `uv python pin 3.x` |
| 加 dev 依赖 | `uv add --dev 包名` |

---

## 9. 故障排查

### `uv sync` 报错"找不到满足要求的 Python"

检查 `.python-version` 存在且里面的版本已安装：

```powershell
cat .python-version
uv python list
```

如果没装对应版本：`uv python install 3.13`。

### `ModuleNotFoundError` 但 `pyproject.toml` 里有这个包

大概率没跑 `uv sync`。跑一次就好。

### VS Code 不识别 `.venv`

`Ctrl+Shift+P` → `Python: Select Interpreter` → 选 `.venv\Scripts\python.exe`。不行就 `Ctrl+Shift+P` → `Developer: Reload Window`。
