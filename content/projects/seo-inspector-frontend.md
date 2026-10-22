---
title: "官网检索器前端"
description: "Vue3 + Vite Web 界面：创建任务、导入关键词、监控采集进度、导出结果，配套官网检索器后端使用。"
summary: "Vue3 + Vite 管理界面：创建检索任务、导入关键词、实时监控进度、导出结果。"
featureimage: "img/seo-inspector-frontend.svg"
repo: "https://git.nbook.icu/kairo/seo-inspector-frontend"
tags: ["Vue 3", "Vite", "JavaScript", "SEO"]
weight: 2
draft: false
---

批量关键词官网可见性检索前端：Vue3 + Vite Web 界面，用于创建任务、导入关键词、监控采集进度、导出结果。与 [seo-inspector-backend](/projects/seo-inspector-backend/) 前后端完全分离。

🔗 **源码仓库**：<https://git.nbook.icu/kairo/seo-inspector-frontend>

## 功能特性

- **4 个页面** — 总览（2×2 平台分块）、所有任务（浏览器标签式、按日期分组）、任务详情、下载中心
- **实时轮询** — 自动刷新任务状态和进度
- **API 地址可配置** — 通过 `VITE_API_BASE` 环境变量适配不同部署环境
- **Vite 开发代理** — 开发模式下 `/api` 和 `/shots` 自动转发到后端

## 目录结构

{{< tree >}}
<ul class="tree">
  <li><code>seo-inspector-frontend/</code>
    <ul>
      <li><code>src/</code>
        <ul>
          <li><code>views/</code><span class="tc">Overview / AllTasks / TaskDetail / Download</span></li>
          <li><code>App.vue</code></li>
          <li><code>main.js</code></li>
          <li><code>router.js</code></li>
          <li><code>api.js</code><span class="tc">API 封装（使用 VITE_API_BASE）</span></li>
          <li><code>style.css</code></li>
        </ul>
      </li>
      <li><code>index.html</code></li>
      <li><code>vite.config.js</code><span class="tc">Vite 配置，含 /api 和 /shots 代理</span></li>
      <li><code>package.json</code></li>
    </ul>
  </li>
</ul>
{{< /tree >}}

## 运行

```bash
npm install
npm run dev          # 开发模式，http://localhost:5173/，vite 自动代理 /api
npm run build        # 构建到 dist/，用任意 Web 服务器（Nginx / Caddy 等）部署静态文件
```

环境要求：Node.js >= 18。
