---
tags: [dashboard]
---

# AkaMaCorp ナレッジダッシュボード

## デイリーログ一覧

```dataview
TABLE file.mtime AS "最終更新"
FROM "memory/daily"
SORT file.name DESC
```

---

## 結晶化チェックポイント

```dataview
TABLE file.mtime AS "実施日"
FROM "memory/crystallization"
SORT file.name DESC
```

---

## エージェント一覧

```dataview
TABLE file.mtime AS "最終更新"
FROM "agents"
WHERE file.name = "CLAUDE"
SORT file.path ASC
```
