---
title: "uv Daily Workflow"
description: "Day-to-day uv usage for existing projects: sync, dependency management, Python versions, and virtual environment maintenance."
date: "2026-08-01"
slug: "uv-daily-workflow"
categories: [开发环境]
tags: ["uv","Python","Command Reference"]
image: "/img/article-covers/uv-daily-workflow.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

This article covers how to use uv **when you sit down to work each day**. For installation, see "uv Development Environment Setup"; for creating a new project, see "uv New Project Workflow".

---

## 1. Opening an existing project

Opening a project daily takes one step: enter the project directory.

```powershell
D:
cd projects\my-analysis
```

No manual virtual environment activation needed — uv finds the right environment automatically via the project's `.python-version` and `.venv`.

> If you prefer VS Code, just open the folder with `code .`; VS Code auto-detects `.venv`.

---

## 2. When to run `uv sync`

Run it in these cases:

| Scenario | Reason |
|------|------|
| Pulled code committed by others (`git pull`) | `pyproject.toml` may have gained new dependencies |
| You edited `pyproject.toml` manually (added a package) | The new package needs installing |
| `.venv` was deleted or corrupted | Reinstall all dependencies |
| New machine / freshly cloned repo | Set up the environment from scratch |

```powershell
uv sync
```

`uv sync` compares `pyproject.toml` with `uv.lock`, installs missing packages, and updates the lock file.

> **No need to run it on every boot** — if dependencies haven't changed, nothing needs reinstalling.

---

## 3. Adding / removing packages

### Adding dependencies

```powershell
# Latest version
uv add polars

# Specific version
uv add "polars>=1.40"

# With extras (e.g. pyarrow)
uv add "polars[pyarrow,numpy]"

# To the dev group (test tools, formatters, etc.)
uv add --dev pytest ruff

# To a custom group
uv add --group lint ruff
```

`uv add` automatically:
1. Updates `pyproject.toml`
2. Updates `uv.lock`
3. Installs the package into `.venv`

### Removing packages

```powershell
uv remove polars
```

Also updates all three automatically.

### Viewing installed packages

```powershell
# Show the project's dependency tree
uv tree

# Show directly declared dependencies from pyproject.toml
uv sync --dry-run
```

---

## 4. Handling uv.lock

`uv.lock` records the **exact versions** of all dependencies (including transitive ones).

**Commit it to Git.** Reason: everyone on the team and every machine installs identical versions, avoiding "it works on my machine" issues.

> Don't edit `uv.lock` manually — let uv manage it.

---

## 5. Python version management

uv stores Python interpreters under `~/.uv/`, **shared across all projects**. `uv python install` is a one-time global action; new projects don't reinstall. Each project declares its version with `uv python pin`.

### Listing installed Pythons

```powershell
uv python list
# Example output:
# cpython-3.13.0-windows-x86_64   .venv\Scripts\python.exe
# cpython-3.13.0-windows-x86_64   C:\Users\you\.uv\python\cpython-3.13.0-...\python.exe
```

### Pinning the project Python version

```powershell
uv python pin 3.13
```

Creates a `.python-version` file. `uv sync` / `uv venv` use this version from then on.

### Switching versions

```powershell
# 1. Install the new version
uv python install 3.14

# 2. Change the pin
uv python pin 3.14

# 3. Sync again (rebuilds .venv)
uv sync
```

---

## 6. Running scripts directly: uv run

`uv run` runs commands **without activating the virtual environment**, automatically using the project's Python and dependencies.

```powershell
# Run a Python script
uv run python script.py

# Run Jupyter notebook
uv run jupyter lab

# Run tests
uv run pytest
```

Great for one-off commands — no environment activation concerns.

> For daily Notebook work, prefer VS Code's Jupyter extension (open `.ipynb` and hit run); `uv run jupyter lab` is the fallback.

---

## 7. Virtual environment maintenance

### Location

`.venv/` lives in the project root; VS Code auto-detects it.

### Manual activation (rarely needed)

```powershell
# PowerShell
.venv\Scripts\Activate.ps1
```

> If you get an error like "running scripts is disabled on this system", run once: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`, then reopen PowerShell. This affects only the current user, is permanent, and does not change system policy.

### Delete and rebuild

```powershell
rm -r -force .venv
uv sync
```

### Package cache

uv caches downloaded packages under `~/.uv/`, shared across projects, so each project doesn't duplicate storage. **No cleanup needed** — uv handles it when full.

---

## 8. Common scenarios quick reference

| What you want | Command |
|-----------|------|
| Open the project | `cd <project dir>`, then `code .` |
| Install dependencies after pulling code | `uv sync` |
| Add a package | `uv add <package>` |
| Remove a package | `uv remove <package>` |
| Run a script | `uv run python script.py` |
| Start a Notebook | `uv run jupyter lab` |
| Run tests | `uv run pytest` |
| Show the dependency tree | `uv tree` |
| Pin the Python version | `uv python pin 3.x` |
| Add a dev dependency | `uv add --dev <package>` |

---

## 9. Troubleshooting

### `uv sync` says "no Python satisfying the requirement found"

Check that `.python-version` exists and that the version in it is installed:

```powershell
cat .python-version
uv python list
```

If the version isn't installed: `uv python install 3.13`.

### `ModuleNotFoundError` but the package is in `pyproject.toml`

Most likely `uv sync` wasn't run. Run it once.

### VS Code doesn't recognize `.venv`

`Ctrl+Shift+P` → `Python: Select Interpreter` → choose `.venv\Scripts\python.exe`. If that doesn't help, `Ctrl+Shift+P` → `Developer: Reload Window`.
