# AkaMaCorp Vault — Claude Code コンテキスト

## このVaultについて
AkaMaCorp は NFD（育成優先型開発）に基づくAIエージェント組織 + ナレッジベース。
Obsidian Vault として管理し、Claude Code で編集する。

## エージェント構成
| # | エージェント | 役割 |
|---|------------|------|
| 01 | secretary | オーケストレーター・知識結晶化 |
| 02 | reviewer | PRレビュー・マージ管理 |
| 03 | designer | Web UI/UX・Figma |
| 04 | sharepoint | SharePoint・ScriptLoader・REST API |
| 05 | frontend | HTML/CSS/JS・QA |
| 06 | discrepancy-check | Excel/Figma差分チェック |

## memory/ の使い方
- `memory/daily/YYYY-MM-DD.md` — 日次ログ（タグ: DECISION/INSIGHT/ERROR/PATTERN）
- `memory/insights.md` — 結晶化候補の洞察リスト
- `memory/error-patterns.md` — 失敗パターン記録
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
