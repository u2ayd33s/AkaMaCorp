---
updated: 2026-05-06
---

# knowledge — ナレッジハブ

AkaMaCorp の全ナレッジを管理するルートINDEX。

---

## フォルダ構成

```
knowledge/
├── INDEX.md                    ← このファイル（全体ハブ）
├── _templates/                 ← テンプレート置き場
│   ├── note.md                 ← notes/ ノート用
│   ├── project-knowledge.md    ← プロジェクトナレッジ用
│   └── agent-lesson.md         ← エージェント学習ログ用
├── notes/                      ← プロジェクト横断ナレッジ（詳細 → [[notes/README]]）
├── SPFx/                       ← SPFx 開発ナレッジ
├── chofu-station-signage/      ← 調布駅サイネージ差分チェック
└── toyota-city-signage/        ← 豊田市サイネージ差分チェック
```

---

## プロジェクト別ナレッジ

| フォルダ | 対象リポジトリ | 担当エージェント | INDEX |
|---------|-------------|----------------|-------|
| [[SPFx/INDEX\|SPFx/]] | `MyCorp/SPFx` | 06_SPFx | [[SPFx/INDEX]] |
| [[chofu-station-signage/INDEX\|chofu-station-signage/]] | `DiscrepancyCheck` | 08_discrepancy-check | [[chofu-station-signage/INDEX]] |
| [[toyota-city-signage/INDEX\|toyota-city-signage/]] | `DiscrepancyCheck` | 08_discrepancy-check | [[toyota-city-signage/INDEX]] |

---

## 横断ナレッジ（notes/）

プロジェクトに依存しない共通知識。詳細一覧 → [[notes/README]]

---

## 運用ルール

- **作業前**: `INDEX.md` → `errors.md` → `workflow.md` の順に読む
- **作業後**: 新知見・エラーを該当ファイルに追記し `updated` 日付を更新
- **新規ノート**: `_templates/` のテンプレートをコピーして作成
- **成果物・スクリプト・画像は置かない**（→ 各リポジトリで管理）
