---
date: 2026-04-14
tags:
  - obsidian
  - settings
  - graph
---

# Graphビュー カラーグループ設定

`.obsidian/graph.json` は `.gitignore` で追跡除外済み。
Obsidianを再インストールしたときや設定リセット後は、以下を参考に再設定する。

## colorGroups 設定値

```json
"colorGroups": [
  {"query": "tag:#💥エラー",               "color": {"a": 1, "rgb": 15750485}},
  {"query": "tag:#💡インサイト",           "color": {"a": 1, "rgb": 5025616}},
  {"query": "path:memory/crystallization", "color": {"a": 1, "rgb": 10233776}},
  {"query": "path:memory/daily",           "color": {"a": 1, "rgb": 16757504}},
  {"query": "path:agents",                 "color": {"a": 1, "rgb": 2201331}},
  {"query": "file:CLAUDE",                 "color": {"a": 1, "rgb": 16739584}},
  {"query": "path:memory/templates",       "color": {"a": 1, "rgb": 7901340}}
]
```

## カラー一覧

| 優先 | クエリ | 色 | HEX |
|------|--------|-----|-----|
| 1 | `tag:#💥エラー` | 🔴 赤 | `#F05555` |
| 2 | `tag:#💡インサイト` | 🟢 緑 | `#4CAF50` |
| 3 | `path:memory/crystallization` | 💜 紫 | `#9C27B0` |
| 4 | `path:memory/daily` | 🟡 黄 | `#FFB300` |
| 5 | `path:agents` | 🔵 青 | `#2196F3` |
| 6 | `file:CLAUDE` | 🟠 オレンジ | `#FF6D00` |
| 7 | `path:memory/templates` | ⚫ グレー | `#78909C` |

## グループ化の方針

- **path** を主軸にフォルダ単位でグループ化（フォルダ構造が意味的に整理済みのため）
- **tag** でコンテンツ種別（エラー・インサイト）を上位優先で色付け
- **file** でランドマークファイル（CLAUDE.md）を強調
- Obsidianは上から順に最初にマッチしたグループを適用する
