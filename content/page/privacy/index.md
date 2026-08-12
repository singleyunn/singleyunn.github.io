---
title: Privacy Policy
slug: privacy-policy
aliases: [privacy]
description: How this static site handles browser preferences, hosting metadata, external links, and the optional CMS interface.
lastmod: "2026-08-03"
comments: false
---

*Last updated: 2026-08-03*

## Scope

This policy covers the public pages of **xizhiyun** at [singleyunn.github.io](https://singleyunn.github.io/) and the `/admin/` CMS entry point.

This is a static Hugo site published through GitHub Pages. It is a practical description of the current configuration, not a promise that future services will have the same data practices. If analytics, comments, forms, newsletters, advertising, payments, or other third-party services are enabled later, this policy should be updated first.

## What the public site does not collect

The current public site does not provide visitor accounts, comments, contact forms, newsletters, advertising, payment processing, or a site-owned user database. It does not intentionally collect or store names, email addresses, IP addresses, uploaded files, or message content through ordinary page visits.

Comments are disabled. No Google Analytics, Microsoft Clarity, advertising pixel, or other site-configured analytics identifier is currently enabled. The site also does not set a first-party cookie for visitors.

GitHub Pages, as the hosting provider, may process technical request information such as an IP address, user agent, or access time under GitHub's own policies. The site owner does not operate that infrastructure or receive a site-owned copy of those logs through this blog.

## Browser storage

The theme stores two local interface preferences in the visitor's browser:

- `StackColorScheme`: the selected light, dark, or automatic color scheme;
- `liquid-stack-language-preference`: the selected English or Simplified Chinese language preference.

These values are stored in browser `localStorage`, not in a site database, and are not submitted to the site owner. Clearing browser storage resets the preferences.

## External links and resources

Articles and navigation may link to GitHub, X, official documentation, model providers, or other external websites. Opening an external link creates a separate interaction governed by that service's privacy policy, terms, cookies, and logging practices. The site owner does not control those services.

The public pages do not load the CMS editor. Visiting `/admin/` does load the Sveltia CMS JavaScript from `unpkg.com`; that administrative interface may communicate with GitHub to authenticate and edit this repository. See the next section before using it.

## CMS and GitHub repository

The CMS is an administrative convenience, not a public submission form. Access uses a GitHub Personal Access Token from an account with write permission to the repository. Sveltia CMS stores that token in the current browser's local storage for subsequent API requests; it is not committed to this repository. Authorized edits are committed to the public `main` branch and can trigger the GitHub Actions and GitHub Pages deployment workflow.

Do not enter passwords, API keys, verification codes, recovery codes, private keys, cookies, or real user data into article content or CMS fields. Anything committed to this public repository can become publicly accessible. GitHub authentication and GitHub's processing of account information are governed by GitHub's own policies; the site owner does not receive your GitHub password or token.

## Data retention and security

The site owner does not maintain a visitor profile or a site-owned comment or analytics database. Public posts, configuration, and media committed to the repository remain in the repository history unless removed through the normal Git and hosting processes. No online system can guarantee absolute security, so do not submit confidential information through public pages or the CMS.

## Changes to this policy

The policy may be updated when the site's hosting, scripts, analytics, comments, CMS, or other data practices change. The effective date at the top of this page indicates the latest revision.

## Contact

For a question about this site or a public-content correction, open an issue or discussion in the [source repository](https://github.com/singleyunn/singleyunn.github.io). Do not post private credentials or sensitive personal information there.
