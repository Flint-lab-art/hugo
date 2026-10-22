---
title: "gogs-diary"
description: "面向自托管 Gogs 的 AI 开发日记生成器：从 Gogs 读取 push 记录，调用 DeepSeek 生成日/周/月/年四级总结，自动写入思源笔记。"
summary: "从 Gogs 拉取 push 记录，调用 DeepSeek 自动生成日/周/月/年四级开发总结，写入思源笔记。"
featureimage: "img/gogs-diary.svg"
repo: "https://git.nbook.icu/kairo/gogs-diary"
tags: ["Python", "uv", "Gogs API", "DeepSeek API", "自动化"]
weight: 4
draft: false
---

每天写完代码 push 到 Gogs 就忘了自己干了啥，回头写周报月报全靠翻聊天记录。这个项目把这件事自动化：读 Gogs 的 push 记录，调 DeepSeek 生成日记，按日/周/月/年四级整理好，直接写进思源笔记。

🔗 **源码仓库**：<https://git.nbook.icu/kairo/gogs-diary>

## 核心功能

- **日记** — 每天跑，把当天 push 记录归纳成一篇简短日记
- **周报** — 每周一跑，总结上一周（周一 ~ 周日）
- **月报** — 每月 1 号跑，总结上一自然月
- **年报** — 每年 1 月 1 号跑，总结上一自然年
- **看 git diff** — 日记模式调 Gogs API 查每个 commit 改了哪些文件，不是干巴巴复述 commit message
- **手写内容识别** — 周/月/年报会读你在思源里手写的笔记，AI 输出不会被循环喂给下一级
- **补历史数据** — `--date 2026-10-18` 可补任意一天/周/月/年的总结
- **prompt 可配置** — 所有模板都在 `prompts.toml`，改文案不用动代码

## 技术亮点

- **数据源分离** — push 记录直读 Gogs SQLite（不走公网 API），改动文件走 Gogs API，手写笔记走思源 API，三路数据按需组合
- **四级递进不循环** — 日记读 push，周报读手写日记，月报读手写周报，年报读手写月报；每级只取"自动生成"标记之前的手写内容，AI 输出不会污染下一级
- **文档树自动维护** — 按 `<年>/<月>/<第X周>/<日期>` 层级自动创建思源文档，周编号以周一为起点，跨月周按周一归档
- **prompt 外置** — 四种周期的提示词全部抽离到 `prompts.toml`，调语气、改字数、换结构都不需要碰代码
- **日志分级保留** — loguru 按模式分文件（`daily_日期.log` 等），保留 90 天

## 运行效果

思源里自动长这样：

{{< tree >}}
<ul class="tree">
  <li><code>daily note/</code>
    <ul>
      <li><code>2026/</code>
        <ul>
          <li><code>年度总结（2025）</code><span class="tc">← 年报</span></li>
          <li><code>10/</code>
            <ul>
              <li><code>月度总结（2026-10）</code><span class="tc">← 月报</span></li>
              <li><code>第1周/</code>
                <ul>
                  <li><code>周报（9月28日 ~ 10月4日）</code></li>
                  <li><code>2026-10-01</code></li>
                </ul>
              </li>
              <li><code>第2周/</code><span class="tc">…</span></li>
              <li><code>第4周/</code>
                <ul>
                  <li><code>2026-10-19</code></li>
                </ul>
              </li>
            </ul>
          </li>
        </ul>
      </li>
    </ul>
  </li>
</ul>
{{< /tree >}}

日记里按仓库分段，大概长这样：

> ### seo-inspector-frontend
> 今天给前端加了 SEO 巡检结果的详情抽屉，点列表项能看到每个 URL 的具体问题。改了 `ResultDrawer.vue` 和 `api.ts`，调了后端的 `/inspect/{id}/detail` 接口。
>
> ### seo-inspector-backend
> 补了详情接口的聚合逻辑，把页面级和资源级问题合并返回。还加了缓存，重复查同一个任务不用重新跑分析。
>
> ### 总结
> 今天主要把巡检结果从列表视图补到了详情视图，前后端各推了一次。功能闭环了，明天可以测一下。

## 快速开始

```bash
git clone https://git.nbook.icu/kairo/gogs-diary.git
cd gogs-diary
uv sync
cp .env.example .env   # 填入 Gogs / DeepSeek / 思源 配置
```

运行：

```bash
uv run python -m gogs_diary.main            # 今天的日记（默认）
uv run python -m gogs_diary.main weekly     # 上周周报
uv run python -m gogs_diary.main monthly    # 上月月报
uv run python -m gogs_diary.main yearly     # 上年年报

# 补历史数据
uv run python -m gogs_diary.main daily --date 2026-10-18
```

搭配 cron 全自动跑：每天 19:00 日记、每周一 09:00 周报、每月 1 号 00:30 月报、每年 1 月 1 号 01:00 年报。

## 依赖

- Python 3.12+
- 能访问 Gogs 的 SQLite 数据库（同服务器或挂载卷）
- 思源笔记实例（HTTP API 可访问）
- DeepSeek API Key
