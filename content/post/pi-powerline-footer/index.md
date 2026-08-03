---
title: "pi-powerline-footer Extension Guide"
description: "Pi's status bar extension: showing model, context, Git, and session information."
date: "2026-08-01"
slug: "pi-powerline-footer"
categories: [AI 工具]
tags: ["Pi","Extension","Terminal"]
image: "/img/article-covers/pi-powerline-footer.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 0.9.0 | License: **none (personal use OK; commercial use requires care)**

## Introduction

pi-powerline-footer replaces Pi's default bottom bar with a Powerline-style status line (inspired by Powerlevel10k / oh-my-pi) plus several UI enhancements:

- **Status line**: model, thinking level (`think:off/med` etc., colorized), path, git branch and change count, context usage (>70% yellow, >90% red warning), tokens, cost, subscription status
- **Welcome overlay**: shows logo, model info, keybindings, and load stats at startup; closes after 30 seconds or any key (can be disabled)
- **Editor stash**: `Alt+S` stashes editor content, lets you ask quickly after clearing, auto-restores when done
- **Working Vibes**: themed loading copy like `/vibe star trek`
- **Fixed editor mode**: chat area scrolls while the editor stays fixed (`/powerline fixed-editor` controls this)
- **Bash mode**: `ctrl+shift+b` keeps a managed shell session; `cd` and exported variables persist across commands, with ghost-suggestion completion
- Presets: `default` / `minimal` / `compact` / `full` / `nerd` / `ascii`

Source: GitHub `nicobailon/pi-powerline-footer` (357★, actively maintained).

> Note: this competes with pi-atelier (both modify the bottom status bar); install one of the two. This extension is the one currently installed locally.

## Installation

```bash
pi install npm:pi-powerline-footer
```

Restart Pi to take effect.

## Uninstallation

```bash
pi remove npm:pi-powerline-footer
```

## Basic usage

Activates automatically; usable with no configuration. Common commands:

| Command | Purpose |
|---|---|
| `/powerline` | Toggle on/off |
| `/powerline default` | Switch presets (also minimal/compact/full/nerd/ascii) |
| `/powerline fixed-editor on\|off\|toggle` | Fixed editor mode (on by default) |
| `/powerline placement above\|below\|toggle` | Main status line above/below the editor |
| `/powerline mouse-scroll on\|off\|toggle` | Mouse scrolling with native link handling |
| `/vibe <theme>` | Set loading copy theme (star trek, pirate, zen, etc.) |
| `/cd <path>` | Change the session working directory (supports `~` and completion) |

Keybindings: `Alt+S` editor stash, `ctrl+shift+b` toggle bash mode, `ctrl+shift+t` collapse related panels (e.g. with rpiv-todo installed).

## Configuration

Written to `~/.pi/agent/settings.json` (or project `.pi/settings.json`):

```json
{
  "showLastPrompt": true,
  "powerline": {
    "preset": "default",
    "fixedEditor": true,
    "scrollAwayCard": true,
    "placement": "above",
    "welcome": true,
    "mouseScroll": true
  }
}
```

Environment variable: `POWERLINE_NERD_FONTS=1` forces Nerd Fonts; `=0` uses the ASCII fallback (set to 0 when Windows terminals lack Nerd Fonts).

## Notes

- Fixed editor mode takes over mouse scrolling; if scrolling conflicts inside multiplexers like tmux, disable with `/powerline fixed-editor off`
- No license: the code is readable and usable, but lacks open-source license legal protection; confirm the author's permission before commercial use
- If pi-atelier is also installed, the two fight over the bottom bar; keep only one

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\pi-powerline-footer\README.md`
- GitHub: https://github.com/nicobailon/pi-powerline-footer
