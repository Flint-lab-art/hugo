---
title: "官网检索器后端"
description: "批量关键词官网可见性检索后端：对指定关键词在百度/必应前 10 页搜索结果中扫描目标官网标识，判断命中、位置与页码，输出 Excel + 截图，多任务并行。"
summary: "FastAPI + Playwright 批量检索官网在百度/必应前 10 页的可见性，多任务并行、Excel + 截图导出。"
featureimage: "img/seo-inspector-backend.svg"
repo: "https://git.nbook.icu/kairo/seo-inspector-backend"
tags: ["Python", "FastAPI", "Playwright", "SQLite", "SEO"]
weight: 1
draft: false
---

批量关键词官网可见性检索后端：对指定关键词，在 **百度 PC / 必应 PC** 两个平台的前 10 页搜索结果中，扫描目标官网标识/域名，判断是否命中、命中位置与页码，输出 Excel + 截图，支持多任务并行、按日期打包下载。**前端完全独立**，后端只提供 REST API。

🔗 **源码仓库**：<https://git.nbook.icu/kairo/seo-inspector-backend>

## 功能特性

- **2 平台** — 百度 PC / 必应 PC
- **FastAPI** — 基于 FastAPI + uvicorn 构建，异步友好，自动生成 OpenAPI 文档
- **无头 Chromium** — Playwright headless 模式，适合服务器部署
- **验证码处理** — 检测到验证码即标记为错误并跳过，不阻塞、不人工干预
- **多任务并行** — 每个任务独立浏览器 profile、独立 SQLite 数据库、独立截图目录，互不干扰
- **完整生命周期** — 每个任务支持「导入关键词 → 开始 → 重试失败项 → 停止 / 清空 / 删除」
- **命中判定** — 前 10 页内命中官网标识/域名即停止该词，输出全局排名与页码；未命中 / 受限 / 解析异常 / 错误分类记录
- **结果导出** — 每任务 Excel、每任务截图 zip、汇总 Excel（每任务一个 sheet）、按日期打包

## 目录结构

{{< tree >}}
<ul class="tree">
  <li><code>seo-inspector-backend/</code>
    <ul>
      <li><code>src/</code>
        <ul>
          <li><code>server.py</code><span class="tc">FastAPI 入口（uvicorn，端口 27532）</span></li>
          <li><code>profile_helper.py</code><span class="tc">每任务独立浏览器 profile 目录管理</span></li>
          <li><code>core/</code>
            <ul>
              <li><code>config.py</code><span class="tc">全局配置（占位示例值，勿填真实品牌）</span></li>
              <li><code>config_local.py</code><span class="tc">本地真实配置（gitignore，不进仓库）</span></li>
              <li><code>baidu.py / bing.py</code><span class="tc">百度 PC / 必应 PC 采集逻辑</span></li>
              <li><code>engine.py</code><span class="tc">Playwright 浏览器上下文封装（headless）</span></li>
              <li><code>db.py</code><span class="tc">每任务 SQLite 读写</span></li>
              <li><code>cookie_tools.py</code><span class="tc">Cookie 体积管理（防御性）</span></li>
              <li><code>logger.py</code><span class="tc">日志</span></li>
            </ul>
          </li>
        </ul>
      </li>
      <li><code>scripts/start_panel.bat</code><span class="tc">一键启动脚本</span></li>
      <li><code>tests/</code><span class="tc">冒烟测试</span></li>
      <li><code>docs/</code><span class="tc">项目文档</span></li>
    </ul>
  </li>
</ul>
{{< /tree >}}

## 设计要点

- 真实品牌信息（目标域名、官网标识）放在 `core/config_local.py`，已被 gitignore，不进仓库、跨分支不冲突
- 服务启动时会自检配置是否仍为占位值，否则打印明显警告——避免所有关键词被静默判定为「未命中」
- 运行时数据（任务数据库、截图、浏览器 profile、日志）全部 gitignore，默认无需登录态

## 运行

```bash
.venv\Scripts\python src/server.py
# http://127.0.0.1:27532/  API 文档：http://127.0.0.1:27532/docs
```

配套前端见 [seo-inspector-frontend](/projects/seo-inspector-frontend/)，通过 `VITE_API_BASE` 对接本后端。
