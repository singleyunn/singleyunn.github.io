---
title: "npm 命令速查"
description: "整理 npm 镜像源、代理、安装、升级和常用项目命令。"
date: "2026-08-01"
slug: "npm-quick-reference"
categories: [命令速查]
tags: ["npm","Node.js","命令速查"]
image: "/img/article-covers/npm-quick-reference.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

## 镜像源

| 操作 | 命令 |
|------|------|
| 查看当前 registry | `npm config get registry` |
| 设置淘宝镜像 | `npm config set registry https://registry.npmmirror.com` |
| 恢复官方源 | `npm config set registry https://registry.npmjs.org` |

## 代理设置

| 操作 | 命令 |
|------|------|
| 查看当前代理 | `npm config get proxy` |
| 设置 HTTP 代理 | `npm config set proxy http://127.0.0.1:7890` |
| 设置 HTTPS 代理 | `npm config set https-proxy http://127.0.0.1:7897` |
| 删除 HTTP 代理 | `npm config delete proxy` |
| 删除 HTTPS 代理 | `npm config delete https-proxy` |

## 安装与卸载（全局）

| 操作 | 命令 |
|------|------|
| 安装包 | `npm install -g <package>` |
| 卸载包 | `npm uninstall -g <package>` |
| 查看已安装的全局包 | `npm list -g --depth=0` |
| 本地安装（当前项目） | `npm install <package>` |

## 网络诊断

| 操作 | 命令 |
|------|------|
| 测试 registry 连通性 | `curl -s --max-time 5 https://registry.npmjs.org/<package>` |
| 查看 npm 配置全貌 | `npm config list` |

---
更新日期：2026-06-12
