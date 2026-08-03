---
title: "VS Code Configuration Explained"
description: "A function-by-function explanation of common VS Code settings, editor experience, and development environment configuration."
date: "2026-08-01"
slug: "vscode-settings"
categories: [开发环境]
tags: ["VS Code","Development Environment","Configuration"]
image: "/img/article-covers/vscode-settings.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Project convention: **all VS Code settings are written to the global user settings, never to a project-level `.vscode/settings.json`**.
> Global file path: `%USERPROFILE%\AppData\Roaming\Code\User\settings.json`
> Precedence: project-level `.vscode/settings.json` > global user settings. This project doesn't use the project level, to avoid overrides that create confusion.

Last updated: 2026-07-14

---

## I. Settings by section

The sections below explain each part of the current global `settings.json`.

### 1. Files and editor basics

```jsonc
"files.associations": { "settings.local.json": "jsonc" },  // highlight this file as JSON with comments
"files.autoSave": "afterDelay",        // auto-save after you stop typing
"files.autoGuessEncoding": true,       // guess encoding when opening files (prevents garbled Chinese)
"editor.fontSize": 15,                 // editor font size (the lowercase "fontsize" key below is a typo; fontSize is the effective one)
"editor.fontLigatures": false,         // disable ligatures (e.g. => doesn't merge into an arrow glyph)
"editor.mouseWheelZoom": true,         // Ctrl+scroll to zoom font
"editor.wordWrap": "on",               // wrap long lines inside the editor
```

> Note: `"editor.fontsize": 16` is an invalid key (the correct one is `fontSize`); VS Code ignores it. The effective value is `"editor.fontSize": 15`. Consider cleaning up this typo.

### 2. Editing experience (animation / formatting / suggestions)

```jsonc
"editor.cursorBlinking": "smooth",              // smooth cursor blinking
"editor.smoothScrolling": true,                 // smooth scrolling in the editor
"editor.cursorSmoothCaretAnimation": "explicit",// smooth caret animation (explicit jumps only)
"editor.formatOnPaste": true,                   // format on paste
"editor.formatOnType": true,                    // format while typing
"editor.formatOnSave": true,                    // format on save
"editor.guides.bracketPairs": true,             // show bracket pair guides
"editor.acceptSuggestionOnEnter": "smart",      // smart accept of suggestions on Enter (avoid accidental commits)
"editor.suggestSelection": "recentlyUsed",      // default suggestion selection: most recently used
"terminal.integrated.smoothScrolling": true,    // smooth scrolling in the terminal
"workbench.list.smoothScrolling": true,         // smooth scrolling in lists
```

### 3. Terminal configuration

```jsonc
"terminal.integrated.profiles.windows": {
    "PowerShell": { "path": "C:\\Program Files\\PowerShell\\7\\pwsh.exe" }, // point to pwsh 7
    "Command Prompt": { "path": "C:\\WINDOWS\\System32\\cmd.exe", ... },
    "Git Bash": { "source": "Git Bash", ... }
},
"terminal.integrated.defaultProfile.windows": "PowerShell",  // default terminal is PowerShell 7
"terminal.integrated.mouseWheelScrollSensitivity": 3,        // terminal scroll sensitivity
"terminal.integrated.gpuAcceleration": "off",                // disable GPU acceleration (prevents rendering glitches)
```

### 4. UI and theme

```jsonc
"workbench.secondarySideBar.defaultVisibility": "visible", // show secondary sidebar by default
"workbench.colorTheme": "Solarized Light",                 // theme
"workbench.startupEditor": "none",                         // no welcome page on startup
"window.dialogStyle": "custom",                            // custom dialog style
"breadcrumbs.enabled": false,                              // disable breadcrumbs
"explorer.confirmDelete": false,                           // no confirmation when deleting files
"explorer.confirmDragAndDrop": false,                      // no confirmation when dragging files
```

### 5. Notebook (Jupyter) output — the key addition this time

```jsonc
"notebook.lineNumbers": "on",              // show line numbers in notebook cells
"notebook.output.wordWrap": true,          // wrap long lines in output (false = horizontal scroll)
"notebook.output.textLineLimit": 9999,     // max lines shown for text output
"notebook.output.scrolling": true,         // overflow becomes a scrollable box instead of truncation
```

**How the three notebook output settings interact (key):**

- `notebook.output.wordWrap`: controls whether **long lines wrap**. `true` = wrap; `false` (default) = no wrap, needs horizontal scroll.
- `notebook.output.textLineLimit`: controls how many lines of text output are **shown by default**.
  - In the source: `type: number, default: 30, minimum: 1`.
  - **`-1` is invalid** (below minimum:1, it falls back), it does not mean "no truncation". To "avoid truncation as much as possible" you must use a large number (here `9999`).
- `notebook.output.scrolling`: per the source description, `textLineLimit` only determines the **scroll height** of the output region when `scrolling` is enabled.
  - Without `scrolling`: output reaches the line limit → shows "truncated + hint".
  - With `scrolling`: output reaches the limit → goes into a **scrollable box**, content fully preserved.
  - So `textLineLimit: 9999` + `scrolling: true` together achieve "keep all long output, boxed in a scrollable area, without stretching the notebook".

> Changes to these notebook settings require a **window reload** to take effect: Command Palette → `Developer: Reload Window`.

### 6. Python / Jupyter

```jsonc
"python.analysis.completeFunctionParens": true, // auto-append parentheses when completing functions
"jupyter.jupyterCommandLineArguments": [],       // Jupyter launch arguments (empty)
```

### 7. Default openers by file extension

```jsonc
"workbench.editorAssociations": {
    "*.csv": "gc-excelviewer-csv-editor",    // csv in a table viewer
    "*.xlsx": "gc-excelviewer-excel-editor", // xlsx in a table viewer
    "*.parquet": "default",
    "*.duckdb": "default",
    "*.json": "default",
    "*.md": "vscode.markdown.preview.editor" // md opens in preview by default
}
```

### 8. Markdown preview

```jsonc
"markdown-preview-enhanced.previewTheme": "vscode.css",
"markdown-preview-enhanced.revealjsTheme": "vscode.css",
"markdown-preview-enhanced.codeBlockTheme": "vscode.css",
```

### 9. Git

```jsonc
"git.enableSmartCommit": true,        // commit all changes directly when nothing is staged
"git.ignoreMissingGitWarning": true,  // don't warn about missing git
```

### 10. Updates and extensions (all auto-update off)

```jsonc
"update.mode": "none",                 // disable VS Code auto-update
"update.showReleaseNotes": false,      // no release notes popup after updates
"extensions.autoUpdate": false,        // extensions don't auto-update
"extensions.autoCheckUpdates": false,  // don't auto-check extension updates
```

### 11. AI / third-party extensions

```jsonc
"chat.disableAIFeatures": true,        // disable built-in Chat AI features
"claudeCode.preferredLocation": "panel", // Claude Code panel docking position
"chatgpt.localeOverride": "zh-CN",     // UI language for a ChatGPT extension
"codebot.*": ...                       // toggles for a code-generation extension (mostly off)
```

### 12. Miscellaneous

```jsonc
"debug.showBreakpointsInOverviewRuler": true, // show breakpoints in the overview ruler
"chat.viewSessions.orientation": "stacked",   // session view stacked layout
"livePreview.defaultPreviewPath": ...         // Live Preview default page (optional migration from project level)
```

---

## II. Changelog

> Append a line here after every global config change, so you can trace back "when, what, and why".

| Date | Change | Keys involved | Reason |
|------|----------|--------|------|
| 2026-07-14 | Added three notebook output settings | `notebook.output.wordWrap: true`, `notebook.output.textLineLimit: 9999`, `notebook.output.scrolling: true` | Wrap long output, avoid truncation, box into scrollable area; `-1` verified invalid in source, hence 9999 |
| 2026-07-14 | Reverted same three settings mistakenly added at project level | Project-level `D:\your-project\.vscode\settings.json` restored to only `livePreview` | Establish "settings always global" convention, avoid project-level overrides |

---

## III. Cleanup suggestions

- `"editor.fontsize": 16` — invalid key (correct is `fontSize`); delete it to avoid confusion.
