---
title: "uv New Project Workflow"
description: "Start from an empty folder: initialize a project with uv, configure dependencies, and make the first commit."
date: "2026-08-01"
slug: "uv-new-project-workflow"
categories: [开发环境]
tags: ["uv","Python","Project Management"]
image: "/img/article-covers/uv-new-project-workflow.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

Create a runnable project from scratch. Follow the steps in order — only the necessary operations, nothing more.

---

## When to use this

- Empty folder → new project
- Migrating a bare Python script to uv management

**Not for**: cloning an existing project from GitHub (that only needs `cd` + `uv sync`; see "uv Daily Workflow").

---

## Full workflow

### Step 1: Create the project directory and enter it

```powershell
# Switch to your project drive (example D:\projects)
D:
cd projects

# Create and enter the project directory
mkdir my-project
cd my-project
```

> Prefer all-lowercase names with hyphens: `my-project` rather than `my_project` or `MyProject`.

---

### Step 2: Initialize the uv project

```powershell
uv init
```

This generates two files:

```
my-project/
├── pyproject.toml    # project config
└── hello.py          # sample script
```

`hello.py` can be deleted or rewritten into your own script.

---

### Step 3: Configure pyproject.toml

Open `pyproject.toml` in VS Code:

```powershell
code pyproject.toml
```

Change it to something like this (adjust as needed):

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "One-line project description"
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

**Field meanings:**

| Field | Purpose |
|------|------|
| `name` | Project name, keep it consistent with the directory name |
| `version` | Current version number, maintained by you |
| `requires-python` | Minimum Python version, use `>=` rather than pinning hard |
| `dependencies` | Packages needed at runtime |
| `dev` group | Dev/test packages, not needed at runtime |

> For a bare Python project (no dependencies), you can leave out `dependencies` for now and add packages one by one with `uv add`.

---

### Step 4: Check and pin the Python version

First check what's already installed:

```powershell
uv python list
```

If the output already contains `cpython-3.13.x-...` (path under `AppData\Roaming\uv\python\`), the version is **globally installed — no need to install again**. Python interpreters are shared across projects; install once.

Otherwise, download one:

```powershell
uv python install 3.13
```

Then pin it for the current project (creates `.python-version`):

```powershell
uv python pin 3.13
```

> **Pinning is required for every project** — without it, `uv sync` picks the highest version satisfying `requires-python` (e.g. if you installed 3.14, it uses 3.14).

---

### Step 5: Install dependencies

```powershell
uv sync
```

This does three things:
1. Installs all dependencies from `pyproject.toml`
2. Generates `uv.lock` (locks exact versions)
3. Creates the `.venv` virtual environment (in the project root)

---

### Step 6: Open in VS Code

```powershell
code .
```

VS Code automatically detects the Python in `.venv`.

Open the terminal (`` Ctrl+` ``) and check:

```powershell
python --version
# Should output 3.13.x
```

If the version is wrong: `Ctrl+Shift+P` → `Python: Select Interpreter` → choose `.venv\Scripts\python.exe`.

---

### Step 7: Write your first script

Create `main.py` in the project:

```python
import polars as pl

df = pl.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
})

print(df)
```

Run it:

```powershell
uv run python main.py
```

Seeing the DataFrame output means the environment works.

---

### Step 8: Wrap up — .gitignore and the first commit

Create `.gitignore` in the project root:

```gitignore
# Virtual environment (generated per machine)
.venv/

# Python cache
__pycache__/
*.pyc

# Jupyter checkpoints
.ipynb_checkpoints/

# OS files
Thumbs.db
.DS_Store
```

First Git commit:

```powershell
git init
git add .
git commit -m "init: project initialization"
```

> **Commit**: `pyproject.toml`, `uv.lock`, `.python-version`, and your code.
> **Don't commit**: `.venv/`, `__pycache__/`.

---

## Workflow at a glance

```powershell
# 1. Create the project
mkdir my-project && cd my-project

# 2. Initialize
uv init

# 3. Edit pyproject.toml (open in VS Code)
code pyproject.toml

# 4. Python version
uv python install 3.13
uv python pin 3.13

# 5. Install dependencies
uv sync

# 6. Open VS Code
code .

# 7. Write code & run
uv run python main.py

# 8. Wrap up
# create .gitignore → git init → git add . → git commit
```

---

## What daily work looks like afterwards

Once the project is set up, day-to-day operations are covered in "uv Daily Workflow". The core commands:
- Add a package → `uv add`
- Remove a package → `uv remove`
- After pulling code → `uv sync`
- Run a script → `uv run`
