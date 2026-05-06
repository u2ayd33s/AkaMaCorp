---
tags: [vault, claude-code-context]
---

# AkaMaCorp Vault — Claude Code コンテキスト

## このVaultについて
AkaMaCorp は NFD（育成優先型開発）に基づくAIエージェント組織 + ナレッジベース。
Obsidian Vault として管理し、Claude Code で編集する。
全体フロー: [[SPRINT-FLOW]] / 全エージェント: [[agent-registry]]

## エージェント構成
| # | エージェント | 役割 |
|---|------------|------|
| 01 | [[01_secretary/CLAUDE\|secretary]] | オーケストレーター・知識結晶化 |
| 02 | [[02_reviewer/CLAUDE\|reviewer]] | PRレビュー・マージ管理 |
| 03 | [[03_obsidian/CLAUDE\|obsidian]] | Obsidian・ナレッジ管理 |
| 04 | [[04_designer/CLAUDE\|designer]] | Web UI/UX・Figma |
| 05 | [[05_sharepoint/CLAUDE\|sharepoint]] | SharePoint・ScriptLoader・REST API |
| 06 | [[06_SPFx/CLAUDE\|spfx]] | SPFx Web パーツ・拡張機能・App Catalog |
| 07 | [[07_frontend/CLAUDE\|frontend]] | HTML/CSS/JS・QA |
| 08 | [[08_discrepancy-check/CLAUDE\|discrepancy-check]] | Excel/Figma差分チェック |

## knowledge/ の使い方

各プロジェクトに関するナレッジを格納する。エージェントは作業前に参照し、作業後に更新する。

| パス | 内容 |
|------|------|
| `knowledge/_templates/` | ナレッジファイルのテンプレート |
| `knowledge/[プロジェクト名]/INDEX.md` | プロジェクト概要・ナレッジ一覧 |
| `knowledge/[プロジェクト名]/workflow.md` | 作業フロー・手順 |
| `knowledge/[プロジェクト名]/csv-rules.md` | データ仕様・フォーマットルール |
| `knowledge/[プロジェクト名]/errors.md` | エラー集・トラブルシューティング |
| `knowledge/[プロジェクト名]/figma-structure.md` | Figmaノード構造（必要に応じて） |

### ナレッジ運用ルール（全エージェント共通）
1. **作業開始前**: 対象プロジェクトの `INDEX.md` → `errors.md` → `workflow.md` の順に読む
2. **作業中に新事象が発生**: メモしておく
3. **作業終了後**: 新しい知見・エラー・ルール変更を該当ファイルに追記し `updated` 日付を更新する

## memory/ の使い方
- [[dashboard]] — ナレッジダッシュボード（INSIGHT/ERROR/デイリーログ一覧）
- `memory/daily/YYYY-MM-DD.md` — 日次ログ（DECISION/INSIGHT/ERROR/PATTERN）
- `memory/insights/` — 個別INSIGHTファイル
- `memory/errors/` — 個別ERRORファイル
- `memory/crystallization/` — 結晶化チェックポイント
- `memory/templates/` — Obsidianテンプレート置き場

## Claude Code がやること
- 作業後は `memory/daily/` に DECISION/INSIGHT/ERROR を記録する
- 結晶化タイミング（月次 or 20件超）で `nfd-crystallize` スキルを実行
- ノートのフロントマターは必ず `date`, `tags` を含める

## フォルダ構造ルール（複雑化防止）

### agents/
- 各エージェントフォルダには `CLAUDE.md` と `skills/` のみ置く
- **ビルド成果物・sppkg展開物・バイナリは置かない**（→ 各リポジトリの sppkg/ で管理）
- 新エージェント追加時は番号プレフィックス付きフォルダを作成し `agents/index.md` を更新する

### memory/
- 日次ログ: `memory/daily/YYYY-MM-DD.md`（命名規則厳守、プレフィックス禁止）
- INSIGHT: `memory/insights/YYYY-MM-DD_タイトル.md`
- ERROR: `memory/errors/YYYY-MM-DD_タイトル.md`
- **ルートに `insights.md` / `error-patterns.md` などの単一集約ファイルを作らない**（サブフォルダで管理）

### knowledge/
- プロジェクト単位でフォルダを作成: `knowledge/[プロジェクト名]/`
- 汎用ノートは `knowledge/notes/` に置く
- **スクリプト本体・sppkg・画像などの成果物は置かない**（→ 各リポジトリで管理）

---

## 記法ルール
- リンク: `[[ノート名]]` を積極使用
- タグ: kebab-case（例: `#sharepoint`, `#ai-skill`）
- ファイル名: 日本語OK、スペースはハイフン
