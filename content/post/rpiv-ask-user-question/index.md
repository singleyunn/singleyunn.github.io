---
title: "rpiv-ask-user-question Extension Guide"
description: "Lets the model pop up a structured questionnaire before important decisions, reducing guessing and rework."
date: "2026-08-01"
slug: "rpiv-ask-user-question"
categories: [AI 工具]
tags: ["Pi","Extension","Interaction"]
image: "/img/article-covers/rpiv-ask-user-question.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

> Updated: 2026-08-01 | Reference version: 2.3.0 | License: MIT

## Introduction

rpiv-ask-user-question gives the model an `ask_user_question` tool: when the model faces a real decision, it pops up a **terminal dialog** (up to 4 questions), each with numbered options plus a free-input line. Your choices return to the model as structured data.

Value: makes the model "ask instead of guess" — spend 15 seconds choosing instead of an hour reworking a wrong assumption. Highly aligned with a "clarify before acting" work principle.

- From the `juicesharp/rpiv-mono` family (547★, actively maintained, still updated as of 07-31); installable standalone
- pi.dev ~33K downloads/month locally

## Installation

```bash
pi install npm:@juicesharp/rpiv-ask-user-question
```

**Restart the Pi session to take effect.**

## Uninstallation

```bash
pi remove npm:@juicesharp/rpiv-ask-user-question
```

## Basic usage

Zero-config; the tool works after restart. The model calls it automatically when it needs a decision, for example:

```text
Add caching to the API client.
```

Instead of choosing a strategy on its own, the model pops up a dialog: ↑/↓ to select, Enter to confirm, `n` to append a comment, Tab to switch between multiple questions, review on the Submit page before submitting, `Esc` to abandon the whole questionnaire.

For free-form answers: `Shift+Enter` for a new line, `Ctrl+G` opens Pi's configured external editor, `Ctrl+U` clears the draft.

## Notes

- Depends on the model proactively calling it; the model may also choose to just execute — if it's often skipped, require "ask before deciding" in your prompt/AGENTS.md
- No layout conflict with powerline's fixed-editor (uses a standard dialog, not widget rendering)

## References

- Installed package README: `%USERPROFILE%\.pi\agent\npm\node_modules\@juicesharp\rpiv-ask-user-question\README.md`
- GitHub: https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-ask-user-question
