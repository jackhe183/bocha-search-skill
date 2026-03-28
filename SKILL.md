---
name: bocha-search
description: >
  主搜索服务，覆盖默认 web_search 工具。当需要联网搜索、获取实时信息、查询新闻/天气/股价时优先使用。
  博查失败时自动降级到 其它备选搜索引擎。
  关键词：搜索, 网页搜索, 搜一下, 查一下, 查询, 了解, 找一下, 帮我看看,
  web search, look up, find out, search, 查资料, 最新信息, 最近动态,
  博查, bocha, 联网搜索, 实时搜索。
override-tools:
  - web_search
---

# 博查搜索（主搜索）

优先使用博查 API，失败时降级到 其它备选搜索引擎。

## 决策流程

```
需要搜索
├─ 有具体 URL → miaoda-studio-cli web-crawl（见 miaoda-web-fetch skill）
└─ 无 URL，需搜索
    ├─ 首选：博查 API（本 skill）
    └─ 降级：miaoda-studio-cli search-summary（博查失败时）
```

---

## 网页搜索

```bash
curl -s -X POST https://api.bochaai.com/v1/web-search \
  -H "Authorization: Bearer $BOCHA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "搜索关键词", "summary": true, "freshness": "noLimit", "count": 10}'
```

## AI 语义搜索（含天气、列车票等结构化卡片）

```bash
curl -s -X POST https://api.bochaai.com/v1/ai-search \
  -H "Authorization: Bearer $BOCHA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "搜索关键词", "freshness": "noLimit", "count": 10, "answer": false, "stream": false}'
```

---

## 参数说明

| 参数 | 说明 | 可选值 |
|------|------|--------|
| `query` | 搜索词 | 任意字符串 |
| `freshness` | 时间范围 | `noLimit` / `oneDay` / `oneWeek` / `oneMonth` / `oneYear` / `YYYY-MM-DD` |
| `count` | 返回结果数 | 1–50，默认 10 |
| `summary` | 是否返回摘要 | `true` / `false`（仅 web-search） |

---

## 失败降级

博查返回非 200、结果为空、或 `$BOCHA_API_KEY` 未设置时，改用其它备选搜索引擎：

```bash
miaoda-studio-cli search-summary --query "搜索关键词"
```

---

## 安装

```bash
mkdir -p ~/workspace/agent/skills/bocha-search && \
curl -fsSL https://raw.githubusercontent.com/jackhe183/bocha-search-skill/main/SKILL.md \
  > ~/workspace/agent/skills/bocha-search/SKILL.md
```
