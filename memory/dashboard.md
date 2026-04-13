---
tags: [dashboard]
---

# AkaMaCorp ナレッジダッシュボード

[[CLAUDE|← Vault トップ]] | [[agents/index|エージェント一覧]] | [[agent-registry|レジストリ]] | [[SPRINT-FLOW|スプリントフロー]]

---

## インサイト — 結晶化待ち

```dataview
TABLE date AS "日付", title AS "タイトル", crystallization-target AS "結晶化先", agents AS "担当"
FROM "memory/insights"
WHERE status = "保留中"
SORT date DESC
```

## インサイト — 結晶化済み

```dataview
TABLE date AS "日付", title AS "タイトル", agents AS "担当"
FROM "memory/insights"
WHERE status = "結晶化済み"
SORT date DESC
```

---

## エラー — 未解決

```dataview
TABLE date AS "日付", title AS "タイトル", agents AS "担当"
FROM "memory/errors"
WHERE status != "解決済み"
SORT date DESC
```

## エラー — 解決済み

```dataview
TABLE date AS "日付", title AS "タイトル", agents AS "担当"
FROM "memory/errors"
WHERE status = "解決済み"
SORT date DESC
```

---

## デイリーログ一覧

```dataview
TABLE insight-count AS "インサイト数", error-count AS "エラー数", decision-count AS "決断数", agents AS "担当エージェント"
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
TABLE role AS "役割", phase AS "担当フェーズ"
FROM "agents"
WHERE file.name = "CLAUDE" AND contains(tags, "agent")
SORT file.path ASC
```
