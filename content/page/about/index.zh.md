---
title: 关于
slug: about
description: 认识 xizhiyun，以及 xizhiyun 这个博客项目。
comments: false
license: false
toc: false
cvPage: true
menu:
  main:
    weight: -90
    params: { icon: user }
---

<style>
.article-header,.article-metadata,.language-switch,.article-translations{display:none!important}
.about-page{max-width:980px;margin:0 auto;padding:2rem 0 4rem;color:var(--card-text-color-main)}
.about-page>*{opacity:0;animation:about-rise .7s cubic-bezier(.16,1,.3,1) forwards}
.about-page>:nth-child(2){animation-delay:.08s}.about-page>:nth-child(3){animation-delay:.14s}.about-page>:nth-child(4){animation-delay:.2s}
@keyframes about-rise{from{opacity:0;transform:translateY(22px)}to{opacity:1;transform:translateY(0)}}
.about-hero,.about-section{padding:clamp(2rem,5vw,3.5rem);margin-bottom:1.6rem;background:var(--card-background);border:1px solid rgba(var(--accent-color-rgb),.22);border-radius:28px;box-shadow:var(--shadow-l1)}
.about-hero{padding:clamp(2.5rem,7vw,5.5rem);background:radial-gradient(circle at 92% 10%,rgba(var(--accent-color-rgb),.2),transparent 34%),var(--card-background)}
.about-kicker{margin:0 0 1.2rem;color:var(--accent-color);font-size:1.3rem;font-weight:750;letter-spacing:.14em;text-transform:uppercase}
.about-hero h1{margin:0;font-size:clamp(3.8rem,9vw,7rem);line-height:1;letter-spacing:-.05em}.about-profile{max-width:780px;margin:2.2rem 0 0;color:var(--card-text-color-secondary);font-size:1.7rem;line-height:1.75}
.about-nav{display:flex;flex-wrap:wrap;gap:.8rem;margin:1.5rem 0 3rem;padding:0;list-style:none}.about-nav a{display:block;padding:.8rem 1.2rem;color:var(--card-text-color-secondary);font-size:1.3rem;font-weight:700;text-decoration:none;background:var(--card-background);border:1px solid rgba(var(--accent-color-rgb),.22);border-radius:999px}.about-nav a:hover{color:var(--accent-color);background:rgba(var(--accent-color-rgb),.07)}
.about-heading{display:flex;align-items:center;gap:1.2rem;margin:0 0 2.5rem;font-size:clamp(2rem,4vw,2.6rem)}.about-heading:before{width:.45rem;height:2.6rem;content:"";background:var(--accent-color);border-radius:999px}
.about-section p,.about-section li{color:var(--card-text-color-secondary);font-size:1.5rem;line-height:1.8}.about-section p:last-child{margin-bottom:0}.about-grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:1rem}.about-card{padding:1.5rem;background:rgba(var(--accent-color-rgb),.07);border:1px solid rgba(var(--accent-color-rgb),.16);border-radius:16px}.about-card h3{margin:0 0 .6rem;color:var(--accent-color);font-size:1.6rem}.about-card p{margin:0;font-size:1.4rem}.about-links{display:grid;gap:.8rem;margin:0;padding:0;list-style:none}.about-links a{display:flex;justify-content:space-between;gap:1rem;padding:1rem 1.2rem;color:var(--card-text-color-main);font-size:1.45rem;text-decoration:none;background:rgba(var(--accent-color-rgb),.07);border-radius:14px}.about-links a:hover{color:var(--accent-color)}.about-links span{color:var(--card-text-color-secondary);font-size:1.3rem}
@media(max-width:700px){.about-page{padding-top:.5rem}.about-grid{grid-template-columns:1fr}.about-links a{display:block}.about-links span{display:block;margin-top:.3rem}}
</style>

<main class="about-page">
<header class="about-hero"><p class="about-kicker">关于页面 · xizhiyun</p><h1>xizhiyun</h1><p class="about-profile">这里是我的个人博客。我会在这里记录 AI 工具、效率方法与数字生活中的实践，也把值得复用的经验整理成更容易理解的内容。</p></header>
<nav class="about-nav" aria-label="页面章节"><a href="#focus">我在关注什么</a><a href="#site">这个站点</a><a href="#contact">找到我</a></nav>
<section id="focus" class="about-section"><h2 class="about-heading">我在关注什么</h2><div class="about-grid"><article class="about-card"><h3>AI 工具</h3><p>记录工具选择、实际使用感受和适合普通人的工作流。</p></article><article class="about-card"><h3>效率实践</h3><p>把零散的操作经验整理成可以照着做的步骤和清单。</p></article><article class="about-card"><h3>数字生活</h3><p>关注账号、设备、订阅和跨平台使用中的真实问题。</p></article><article class="about-card"><h3>持续记录</h3><p>不追求一次写完，先把验证过的内容留下，再持续修正。</p></article></div></section>
<section id="site" class="about-section"><h2 class="about-heading">这个站点</h2><p>“xizhiyun”是一个持续建设中的个人博客。站点使用 Hugo、Liquid Stack、GitHub Actions 和 GitHub Pages 搭建，内容会随着真实使用逐步补充。</p><p>这里的文章、启动台、照片墙和链接页都会慢慢替换成我自己的内容；如果某个页面还保留示例，它只是临时占位，不代表最终内容。</p></section>
<section id="contact" class="about-section"><h2 class="about-heading">找到我</h2><ul class="about-links"><li><a href="https://xizhiyun1995-netizen.github.io/zh/" rel="me">博客主页 <span>xizhiyun1995-netizen.github.io/zh/</span></a></li><li><a href="https://github.com/xizhiyun1995-netizen" target="_blank" rel="me noopener">GitHub <span>@xizhiyun1995-netizen</span></a></li><li><a href="mailto:xizhiyun1995@gmail.com">邮箱 <span>xizhiyun1995@gmail.com</span></a></li><li><a href="https://x.com/Singlea8uw" target="_blank" rel="me noopener">X <span>@Singlea8uw</span></a></li></ul></section>
</main>
