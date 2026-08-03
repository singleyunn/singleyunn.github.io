---
title: "uv 新开项目全流程"
description: "从空文件夹开始，用 uv 初始化项目、配置依赖并完成首次提交。"
date: "2026-08-01"
slug: "uv-new-project-workflow"
categories: [开发环境]
tags: ["uv","Python","项目管理"]
image: "/img/article-covers/uv-new-project-workflow.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

从零开始创建一个能跑的项目，跟着顺序做即可。每一步只写必要的操作，不多不少。

---

## 适用场景

- 空文件夹 → 新项目
- 想把一个裸 Python 脚本迁移到 uv 管理

**不适用**：从 GitHub 克隆已有项目（那种情况只需 `cd` + `uv sync`，见《uv-日常使用说明》）。

---

## 完整流程

### 第 1 步：创建项目目录并进入

```powershell
# 切到你的项目盘（示例 D:\projects）
D:
cd projects

# 创建并进入项目目录
mkdir my-project
cd my-project
```

> 项目名建议全小写、用短横线：`my-project` 而非 `my_project` 或 `MyProject`。

---

### 第 2 步：初始化 uv 项目

```powershell
uv init
```

生成两个文件：

```
my-project/
├── pyproject.toml    # 项目配置
└── hello.py          # 示例脚本
```

`hello.py` 可以删除或改写成你自己的脚本。

---

### 第 3 步：配置 pyproject.toml

用 VS Code 打开 `pyproject.toml`：

```powershell
code pyproject.toml
```

把内容改成类似下面这样（按需调整）：

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "项目一句话描述"
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
    "ruff>=0.5",
]
```

**各字段含义：**

| 字段 | 作用 |
|------|------|
| `name` | 项目名，与目录名保持一致 |
| `version` | 当前版本号，自己维护 |
| `requires-python` | Python 版本下限，用 `>=` 不写死 |
| `dependencies` | 运行所需的包 |
| `dev` 组 | 开发/测试用的包，不影响运行 |

> 如果你只需要一个裸 Python 项目（没有依赖），可以暂时不写 `dependencies`，以后用 `uv add` 逐个加。

---

### 第 4 步：检查并固定 Python 版本

先看是否已经安装过：

```powershell
uv python list
```

如果输出里已经有 `cpython-3.13.x-...`（路径在 `AppData\Roaming\uv\python\`），说明**全局已装，不需要再装**。Python 解释器是所有项目共享的，装一次就行。

如果没有，下载一份：

```powershell
uv python install 3.13
```

然后固定到当前项目（生成 `.python-version`）：

```powershell
uv python pin 3.13
```

> **pin 是每个项目必须做的**，不 pin 的话 `uv sync` 会在所有满足 `requires-python` 的版本里选最高的（比如你装了 3.14，它就用 3.14）。

---

### 第 5 步：安装依赖

```powershell
uv sync
```

这会做三件事：
1. 根据 `pyproject.toml` 安装所有依赖
2. 生成 `uv.lock` 文件（锁定精确版本）
3. 创建 `.venv` 虚拟环境（在项目根目录）

---

### 第 6 步：用 VS Code 打开

```powershell
code .
```

VS Code 会自动识别 `.venv` 里的 Python。

打开终端（`` Ctrl+` ``），检查：

```powershell
python --version
# 应输出 3.13.x
```

如果版本不对：`Ctrl+Shift+P` → `Python: Select Interpreter` → 选 `.venv\Scripts\python.exe`。

---

### 第 7 步：写你的第一个脚本

在项目里新建 `main.py`：

```python
import polars as pl

df = pl.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
})

print(df)
```

运行：

```powershell
uv run python main.py
```

看到 DataFrame 输出说明环境正常。

---

### 第 8 步：收尾——.gitignore 与首次提交

项目根目录新建 `.gitignore`：

```gitignore
# 虚拟环境（每台机器自己生成）
.venv/

# Python 缓存
__pycache__/
*.pyc

# Jupyter 检查点
.ipynb_checkpoints/

# 操作系统
Thumbs.db
.DS_Store
```

首次 Git 提交：

```powershell
git init
git add .
git commit -m "init: 项目初始化"
```

> **要提交的文件**：`pyproject.toml`、`uv.lock`、`.python-version`、你的代码。
> **不要提交**：`.venv/`、`__pycache__/`。

---

## 流程一览（速查）

```powershell
# 1. 创建项目
mkdir my-project && cd my-project

# 2. 初始化
uv init

# 3. 编辑 pyproject.toml（VS Code 打开改）
code pyproject.toml

# 4. Python 版本
uv python install 3.13
uv python pin 3.13

# 5. 安装依赖
uv sync

# 6. 打开 VS Code
code .

# 7. 写代码 & 运行
uv run python main.py

# 8. 收尾
# 新建 .gitignore → git init → git add . → git commit
```

---

## 以后日常做什么

项目建好之后，日常操作见《uv-日常使用说明》。核心就是：
- 加包 → `uv add`
- 删包 → `uv remove`
- 拉代码后 → `uv sync`
- 运行脚本 → `uv run`
