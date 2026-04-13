---
tags: [dashboard]
---

# AkaMaCorp ナレッジダッシュボード

[[CLAUDE|← Vault トップ]] | [[agents/index|エージェント一覧]] | [[agent-registry|レジストリ]] | [[SPRINT-FLOW|スプリントフロー]]

---

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
TABLE insight-count AS "INSIGHT数", error-count AS "ERROR数", decision-count AS "DECISION数"
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
