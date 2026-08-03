---
title: Configure Waline Comments
description: Keep the comment interface and safely connect your own Waline service.
date: 2026-07-22
slug: configure-waline-comments
categories: [Tutorials]
tags: [Waline, Comments, Configuration]
---

Liquid Stack keeps the Waline comment component, while its example URL is not connected to a real backend.

## Prepare a server

Deploy your own service by following the official Waline documentation. When you have its URL, replace the sample value at `params.comments.waline.serverURL` in `hugo.yaml`.

## Enable or disable comments

Set `params.comments.enabled` to `true` to enable comments. Individual posts can still use `comments: false`, which is useful for privacy pages, documentation, or announcements.

## Test before release

Check that the comment box appears below a post locally, then test submission, moderation, and notifications after deployment. Keep server URLs, tokens, and administrator settings in your own environment rather than in a public theme example.
