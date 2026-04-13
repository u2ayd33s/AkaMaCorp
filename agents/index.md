---
tags: [index, agents]
---

# エージェント インデックス

[[CLAUDE|← Vault トップ]] / [[agent-registry|全エージェント詳細]]

---

## エージェント一覧

```dataview
TABLE role AS "役割", phase AS "担当フェーズ", skills AS "スキル"
FROM "agents"
WHERE file.name = "CLAUDE" AND tags != null AND contains(tags, "agent")
SORT file.path ASC
```

---

## スキル一覧

```dataview
TABLE file.folder AS "エージェント", description AS "概要"
FROM "agents"
WHERE file.name = "SKILL"
SORT file.path ASC
```

---

## エージェント別スキル数

```dataview
TABLE length(skills) AS "スキル数", role AS "役割"
FROM "agents"
WHERE file.name = "CLAUDE" AND contains(tags, "agent")
SORT file.path ASC
```
