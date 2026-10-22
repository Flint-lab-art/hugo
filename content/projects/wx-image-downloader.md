---
title: "wx-image-downloader"
description: "一键下载微信公众号文章的封面图和正文所有图片：粘贴链接 → 预览缩略图 → 勾选下载 → 打包 ZIP。"
summary: "粘贴公众号文章链接，一键预览并下载封面图与正文图片，打包 ZIP，跨平台。"
featureimage: "img/wx-image-downloader.svg"
repo: "https://git.nbook.icu/kairo/wx-image-downloader"
tags: ["Node.js", "Vue 3", "Fastify", "Naive UI", "工具"]
weight: 3
draft: false
---

一键下载微信公众号文章的封面图和正文所有图片。粘贴文章链接 → 预览缩略图 → 勾选下载 → 打包 ZIP。跨平台（Windows / macOS / Linux，需 Node.js）。

🔗 **源码仓库**：<https://git.nbook.icu/kairo/wx-image-downloader>

## 功能特点

- **批量粘贴** — 每行一个文章链接，一次处理多篇
- **自动封面** — 优先提取 `og:image` meta，兜底取正文第一张图
- **兼容懒加载** — 正确读取 `data-src`（微信懒加载图片的真实地址）
- **原图下载** — 自动清理微信懒加载参数（`wxfrom` / `wx_lazy` / `wx_co` / `tp` / `wx_header`），请求原图
- **SVG 轮播帧提取** — 识别公众号横向滑动轮播 SVG，逐帧提取保存
- **防盗链绕过** — 后端代理图片请求，自动注入正确的 `Referer` 头
- **缩略图预览** — 浏览器内预览所有图片，点击勾选 / 取消
- **并发下载** — 5 路并发，指数退避重试 3 次
- **ZIP 打包** — 内存中打包，浏览器直接下载

## 技术栈

| 层 | 技术 |
|---|---|
| 前端 | Vue 3.5 + naive-ui + Vite 6 |
| 后端 | Fastify 5 + undici + cheerio |
| 打包 | JSZip（内存打包） |
| 包管理 | pnpm 12.x |

## 运行

Windows 双击 `start.bat` 自动检测 pnpm、安装依赖并启动服务。手动方式：

```bash
pnpm install
pnpm dev      # 开发模式（Vite :5173，API :3000，热更新）
pnpm start    # 生产模式（Fastify 单端口服务 API + 静态文件，监听 :3000）
```

浏览器访问 http://localhost:3000 。后端只监听 `127.0.0.1`，不对外网暴露。

## 项目结构

{{< tree >}}
<ul class="tree">
  <li><code>wx-image-downloader/</code>
    <ul>
      <li><code>index.html</code><span class="tc">Vite 入口</span></li>
      <li><code>vite.config.js</code><span class="tc">Vite 配置（/api 代理到 :3000）</span></li>
      <li><code>server.js</code><span class="tc">Fastify 后端（全部逻辑）</span></li>
      <li><code>start.bat</code><span class="tc">Windows 一键启动</span></li>
      <li><code>src/</code><span class="tc">Vue 入口 + 单页 UI</span></li>
      <li><code>python-legacy/</code><span class="tc">旧版 Python CLI + tkinter GUI（已归档）</span></li>
      <li><code>docs/</code><span class="tc">项目文档</span></li>
    </ul>
  </li>
</ul>
{{< /tree >}}
