---
tags: [dashboard]
---

# AkaMaCorp ナレッジダッシュボード

## INSIGHT — 結晶化待ち

```dataview
TABLE date AS "日付", title AS "タイトル", crystallization-target AS "結晶化先"
FROM "memory/insights"
WHERE status = "pending"
SORT date DESC
```

## INSIGHT — 結晶化済み

```dataview
TABLE date AS "日付", title AS "タイトル"
FROM "memory/insights"
WHERE status = "crystallized"
SORT date DESC
```

---

## ERROR — 未解決

```dataview
TABLE date AS "日付", title AS "タイトル"
FROM "memory/errors"
WHERE status != "resolved"
SORT date DESC
```

## ERROR — 解決済み

```dataview
TABLE date AS "日付", title AS "タイトル"
FROM "memory/errors"
WHERE status = "resolved"
SORT date DESC
```

---

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
