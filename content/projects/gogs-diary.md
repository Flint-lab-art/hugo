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

面向自托管 **Gogs** 的 AI 开发日记生成器。从 Gogs 的 SQLite 数据库读取 push 记录，调用 DeepSeek 生成 **日 / 周 / 月 / 年** 四级总结，自动写入思源笔记。

🔗 **源码仓库**：<https://git.nbook.icu/kairo/gogs-diary>

## 功能

- **日记** — 每天跑，把当天 push 记录归纳成一篇简短日记
- **周报** — 每周一跑，总结上一周（周一 ~ 周日）
- **月报** — 每月 1 号跑，总结上一自然月
- **年报** — 每年 1 月 1 号跑，总结上一自然年
- **看 git diff** — 日记模式会调 Gogs API 查每个 commit 改了哪些文件，写出来的日记更具体
- **prompt 可配置** — 所有 prompt 模板都在 `prompts.toml`，改文案不用动代码

## 思源文档树结构

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

- 周编号规则：以周一为一周开始，1 号所在周为第 1 周；跨月的周按周一所在月份归档。

## 部署

```bash
git clone https://git.nbook.icu/kairo/gogs-diary.git
cd gogs-diary
uv sync
```

复制 `.env.example` 为 `.env` 配置 Gogs 数据库路径、Gogs API、DeepSeek、思源笔记后即可运行：

```bash
uv run python -m gogs_diary.main            # 今天的日记（默认）
uv run python -m gogs_diary.main weekly     # 上周周报
uv run python -m gogs_diary.main monthly    # 上月月报
uv run python -m gogs_diary.main yearly     # 上年年报
```

搭配 cron 即可完全自动化：每天 23:50 日记、每周一 09:00 周报、每月 1 号 00:30 月报、每年 1 月 1 号 01:00 年报。

## 依赖

- Python 3.12+
- 能访问 Gogs 的 SQLite 数据库（同服务器或挂载卷）
- 思源笔记实例（HTTP API 可访问）
- DeepSeek API Key
