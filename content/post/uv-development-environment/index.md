---
title: "Setting Up a uv Development Environment (Windows)"
description: "Build a working development environment step by step — VS Code, uv, Python, and virtual environments."
date: "2026-08-01"
slug: "uv-development-environment"
categories: [开发环境]
tags: ["uv","Python","Windows"]
image: "/img/article-covers/uv-development-environment.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

From scratch, every step spelled out. Follow along and it will run.

---

## 1. Install VS Code

**Via winget (install to D:):**

```powershell
winget install Microsoft.VisualStudioCode --location D:\tools\VSCode
```

PATH is added automatically, no restart needed. Open "Visual Studio Code" from the Start menu.

> `--location D:\tools\VSCode` sets the install directory so it doesn't consume C: drive space. The directory is created automatically if missing.
>
> If winget is unavailable, download the MSI installer from https://code.visualstudio.com and pick `D:\tools\VSCode` as the custom install directory.

---

## 2. Install essential VS Code extensions

Open VS Code, click the Extensions icon (or `Ctrl+Shift+X`), and install these 4:

| Search term | Extension | Purpose |
|-----------|--------|------|
| `Chinese` | **Chinese (Simplified) Language Pack** | Chinese UI |
| `Python` | **Python** (Microsoft) | Python language support |
| `Pylance` | **Pylance** (Microsoft) | Code completion / type checking |
| `Jupyter` | **Jupyter** (Microsoft) | Notebook support |

Restart VS Code after installing the language pack; the UI becomes Chinese.

> If the extension marketplace is unreachable (search shows `Failed to fetch`), click `...` in the top-right → "Install from VSIX..." → install a `.vsix` file obtained elsewhere offline.

---

## 3. Install uv

Open PowerShell and run the official install script (auto-downloads and adds PATH):

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

> `-ExecutionPolicy ByPass` bypasses the execution policy check so the system doesn't block the script. It only applies to this command; it does not change system configuration.

Close the current PowerShell window, open a new one, and verify:

```powershell
uv --version
# Output similar to: uv 0.7.x
```

> Only the uv **CLI tool** PATH is added. Python interpreters are downloaded and managed by uv (stored under `~/.uv/`); they don't need PATH and should **not** be added — uv finds them itself. Adding them manually can confuse the `python` command with uv-managed interpreters.

---

## 4. Create a project

PowerShell defaults to `C:\Users\<your-username>`; create the project on D: or another drive instead:

```powershell
# Switch to D: (keep projects off C: so reinstalling the OS doesn't lose them)
D:
# Create a projects folder (mkdir = new folder)
mkdir projects
# Enter that folder (cd = change directory)
cd projects

# Create the project (name it yourself)
uv init my-analysis
cd my-analysis
```

Generated files:

```
my-analysis/
├── pyproject.toml    # project config file (core)
└── hello.py          # sample script (can be deleted)
```

---

## 5. pyproject.toml configuration

uv uses `pyproject.toml` to know which packages to install and which Python version to use. Here is a standard config for a Polars learning project:

```toml
[project]
name = "polars-learning"
version = "0.1.0"
description = "Polars data processing learning project"
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

**Dependency notes:**

| Package | Purpose |
|---|---|
| `polars[pyarrow,numpy]` | Core library + pyarrow extra (read/write Parquet/Arrow) + numpy integration (convert to/from numpy arrays) |
| `matplotlib` | Basic visualization |
| `seaborn` | Advanced statistical charts (depends on matplotlib; no need to list it again) |
| `jupyter` | Notebook environment, used directly in VS Code |
| `pytest` (dev group) | Test framework, dev-only, not packaged into the product |

> `requires-python = ">=3.13"` + `uv python pin 3.13` pins the version. Prefer `>=` as a lower bound; uv generates `uv.lock` to lock exact versions so every install is consistent.

---

## 6. Install Python

```powershell
# Install Python 3.13 (uv downloads it; independent of any system Python)
uv python install 3.13

# Pin this version for the project (important! without this, uv sync may pick the wrong version)
uv python pin 3.13

# List installed Pythons
uv python list
```

> `uv python pin` creates a `.python-version` file in the project. From then on `uv sync` / `uv venv` use that version automatically. **Always pin before sync**, otherwise uv picks the highest version satisfying `requires-python` (e.g. if you installed 3.14, it uses 3.14).

---

## 7. Managing dependencies

```powershell
# Add packages (updates pyproject.toml and uv.lock automatically)
uv add polars seaborn

# Add dev tools (goes into the dev group)
uv add --dev pytest

# Remove a package
uv remove seaborn

# Install everything from pyproject.toml (creates .venv on first run)
uv sync
```

> `uv sync` is the everyday command — run it after editing pyproject.toml. The first run generates `uv.lock`, locking exact versions of all dependencies for consistent installs. Commit this file to Git.

---

## 8. Virtual environment and VS Code

`uv sync` automatically creates `.venv\` in the project. After opening the project folder in VS Code, press `Ctrl+Shift+P` → `Python: Select Interpreter` → choose `.venv\Scripts\python.exe`. The VS Code terminal, run button, and Jupyter extension will all use this environment automatically — no manual activation needed.

---

## 9. Troubleshooting

### After sync, Python is not the version I wanted

You installed `uv python install 3.13`, but `.venv\Scripts\python.exe --version` returns 3.14.

**Cause:** `uv python pin 3.13` was not run first. `uv sync` picks the highest version satisfying `requires-python`.

**Fix:**

```powershell
# 1. Remove the wrong .venv
rm -r -force .venv
rm uv.lock

# 2. Pin the version (don't forget this time)
uv python pin 3.13

# 3. Sync again
uv sync
```

### VS Code Jupyter kernel list doesn't show the new .venv Python

`Ctrl+Shift+P` → "Python: Select Interpreter" → browse to `.venv\Scripts\python.exe` manually.

Still not showing after selecting: `Ctrl+Shift+P` → "Developer: Reload Window".

---

## Common commands quick reference

```powershell
# ---- Project ----
uv init <name>              # Create a new project
uv add <package>            # Add a dependency
uv add --dev <package>      # Add a dev dependency
uv remove <package>         # Remove a dependency
uv sync                     # Install all dependencies from pyproject.toml

# ---- Python ----
uv python install 3.13      # Install Python
uv python list              # List installed Pythons
uv python pin 3.13          # Pin the project Python version
```
