---
name: bocha-search
description: >
  使用博查（Bocha AI）搜索互联网。当用户需要联网搜索、获取实时信息、查询新闻/天气/股价等场景时使用。
  在 Claude Code 环境中通过 curl 直接调用，无需任何额外安装。
  关键词：博查、bocha、联网搜索、实时搜索、网络搜索。
---

# 博查搜索

通过 curl 直接调用博查 API，无需安装任何依赖。

**前提**：环境变量 `BOCHA_API_KEY` 已设置，或在命令中替换为实际 Key。

---

## 网页搜索

```bash
curl -s -X POST https://api.bochaai.com/v1/web-search \
  -H "Authorization: Bearer $BOCHA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "搜索关键词",
    "summary": true,
    "freshness": "noLimit",
    "count": 10
  }'
```

## AI 语义搜索（含天气、列车票等结构化卡片）

```bash
curl -s -X POST https://api.bochaai.com/v1/ai-search \
  -H "Authorization: Bearer $BOCHA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "搜索关键词",
    "freshness": "noLimit",
    "count": 10,
    "answer": false,
    "stream": false
  }'
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

## 解析结果（Python 一行）

```bash
curl -s ... | python3 -c "
import json,sys
data=json.load(sys.stdin)
for r in data.get('data',{}).get('webPages',{}).get('value',[]):
    print(r['name'], r['url'])
"
```
