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
| 06 | [[06_frontend/CLAUDE\|frontend]] | HTML/CSS/JS・QA |
| 07 | [[07_discrepancy-check/CLAUDE\|discrepancy-check]] | Excel/Figma差分チェック |

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

## 記法ルール
- リンク: `[[ノート名]]` を積極使用
- タグ: kebab-case（例: `#sharepoint`, `#ai-skill`）
- ファイル名: 日本語OK、スペースはハイフン
