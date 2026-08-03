---
title: "How I Manage Skills: Project-Local Installation Plus Symlinks"
description: "A geek-flavored way to manage Agent Skills: install only what you need per project, then link to a single canonical copy with symlinks to save context and update once."
date: "2026-08-03"
slug: "manage-skills-symlinks"
categories: [开发环境]
tags: ["Skills","Symlinks","Context Optimization","Claude Code","Workflow"]
image: "/img/article-covers/manage-skills-symlinks.svg"
toc: true
comments: false
license: false
---

Here is how I manage Agent Skills — geek-flavored, not for everyone, but hopefully a useful idea.

## 1. Install Skills per project, not globally

Agent Skills can be installed globally (shared by all projects) or per project (only usable in the current project). I choose project-local installs mainly to save context space.

An Agent has a context window — think of it as Claude's workbench with a finite surface. Skills only load their name and description by default (full content is not spread out), but it adds up: install dozens of Skills globally and even the summaries alone eat a large chunk of the workbench. Worse, once Claude decides a Skill is relevant to the current task, it loads the full content. The more global Skills you have, the higher the chance of accidental triggering — a pure waste of space.

Install only the Skills a project truly needs. Then the workbench only holds what is relevant to the current work, leaving precious space for more important content, and Claude works more efficiently.

## 2. Install Skills via symlinks

This is the core of my approach. First, what is a symlink?

Think of it as a Windows shortcut — the file itself exists once, but you can create shortcuts to it in many places. Change the original and every shortcut points to the updated content automatically.

Three steps:

### Step 1: Download open-source Skills projects to a unified directory

I keep a dedicated folder for GitHub projects, `~/GitHub`. All downloaded open-source projects live there:

```text
~/GitHub/baoyu-skills      ← open-source project hosting various Skills
~/GitHub/baoyu-design      ← another open-source project
```

This folder acts like a warehouse; all Skill originals are stored here.

### Step 2: Create symlinks inside your own projects

Say I have a writing project `~/GitHub/baoyu-writing` that needs several Skills. I do not copy the Skills in — I create symlinks so the project points to the originals in the warehouse:

```text
path inside the project              →  actual location (original)
.agents/skills/baoyu-comic          →  ~/GitHub/baoyu-skills/skills/baoyu-comic
.agents/skills/baoyu-design         →  ~/GitHub/baoyu-design/skills/baoyu-design
```

### Step 3: Create one entry point for Claude Code

Finally, create one more symlink so Claude Code can find the Skills:

```text
.claude/skills  →  .agents/skills
```

Claude Code can then follow this chain to all needed Skills.

## 3. No need to memorize commands — let the Agent do it

Worried you cannot remember symlink commands?

You do not need to. Just tell Codex/Claude Code in natural language, e.g.:

```text
Symlink ~/GitHub/baoyu-skills/skills/baoyu-comic to .agents/skills/baoyu-comic
```

Or even simpler:

```text
Link the baoyu-comic skill from the baoyu-skills project into the current project
```

The Agent creates the symlinks for you. Maintenance, adding, and removing can all be delegated. You only say which Skill links where; the Agent does the dirty work.

## 4. Why is it worth the trouble?

Initial setup takes a few more minutes than copy-paste, but maintenance becomes very cheap. Two main benefits:

Benefit one: update once. Because every project points to the same originals via symlinks, when the open-source project updates, just pull the latest code in `~/GitHub/baoyu-skills` — every project using that Skill is automatically on the new version.

Benefit two: bug fixes flow back upstream. If I find an issue while using the comic Skill in a writing project, I just ask the Agent to fix it. Because of the symlink, the Agent edits the original in the warehouse (`~/GitHub/baoyu-skills/skills/baoyu-comic`), and I can commit the fix back to the open-source project — a free contribution to the community.

## Reference

- Original author: 宝玉 (@dotey)
- Source:

```text
https://x.com/dotey/status/2069632132431929651
```
