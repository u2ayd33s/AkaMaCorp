---
updated: 2026-05-03
---

# knowledge — ナレッジハブ

AkaMaCorp の全ナレッジを管理するルートINDEX。

---

## フォルダ構成

```
knowledge/
├── INDEX.md                    ← このファイル（全体ハブ）
├── _templates/                 ← テンプレート置き場
├── notes/                      ← プロジェクト横断ナレッジ
├── SPFx/                       ← SPFx 開発ナレッジ
├── chofu-station-signage/      ← 調布駅サイネージ差分チェック
└── toyota-city-signage/        ← 豊田市サイネージ差分チェック
```

---

## プロジェクト別ナレッジ

| フォルダ | 対象リポジトリ | 担当 | INDEX |
|---------|-------------|------|-------|
| [[SPFx/INDEX\|SPFx/]] | `MyCorp/SPFx` | 06_SPFx | [[SPFx/INDEX]] |
| [[chofu-station-signage/INDEX\|chofu-station-signage/]] | `DiscrepancyCheck` | 08_discrepancy-check | [[chofu-station-signage/INDEX]] |
| [[toyota-city-signage/INDEX\|toyota-city-signage/]] | `DiscrepancyCheck` | 08_discrepancy-check | [[toyota-city-signage/INDEX]] |

---

## notes/ — 横断ナレッジ

プロジェクトに依存しない共通知識。SharePoint・デザイン・API リファレンスなど。

→ 詳細は [[notes/README]]

### SharePoint

| ファイル | 種別 | 内容 |
|---------|------|------|
| [[notes/Graph vs sharepoint rest api\|Graph vs SharePoint REST API]] | 📚reference | Graph API と SharePoint REST API の使い分け |
| [[notes/SharePointページコメントREST-API\|SharePoint ページコメント REST API]] | 📚reference | コメント取得・投稿の REST API 仕様 |
| [[notes/SharePoint-REST-API-フィールド一覧\|SharePoint REST API フィールド一覧]] | 📚reference | リスト/ライブラリで取得できるフィールド一覧 |
| [[notes/SharePointページ・ニュース一括再公開マニュアル\|一括再公開マニュアル（フロントマター版）]] | 📖how-to | PnP PowerShell による一括再公開手順 |
| [[notes/SharePoint一括再公開マニュアル\|一括再公開マニュアル（詳細版）]] | 📖how-to | 同上・詳細スクリプト付き完全版（実績：267ページ/6分） |

### Fluent UI / デザイントークン

| ファイル | 種別 | 内容 |
|---------|------|------|
| [[notes/SharePoint-FluentUI-DesignTokens\|Fluent UI デザイントークン]] | 📚reference | SharePoint Fluent UI v3 デザイントークン一覧 |
| [[notes/SharePoint-FluentUI-CSS変数リファレンス\|Fluent UI CSS変数リファレンス]] | 📚reference | CSS変数の完全リファレンス |

### デザイン

| ファイル | 種別 | 内容 |
|---------|------|------|
| [[notes/AI生成デザインの「没個性化」問題と脱却ガイド\|AI生成デザイン没個性化問題と脱却ガイド]] | 💡insight | AI デザインの画一化を避けるためのガイド |

---

## SPFx/ — SPFx 開発ナレッジ

| ファイル | 内容 |
|---------|------|
| [[SPFx/setup-and-preview\|setup-and-preview]] | 環境構築・プロジェクト生成・ローカルプレビュー手順 |
| [[SPFx/custom-html-webpart\|custom-html-webpart]] | カスタムHTML Webパーツの実装・デプロイ知見 |
| [[SPFx/sharepoint-theme-color-palette\|sharepoint-theme-color-palette]] | Fluent UI v3 テーマカラーパレット分析（Light/Dark 8テーマ） |

---

## _templates/ — テンプレート

| ファイル | 用途 |
|---------|------|
| [[_templates/project-knowledge\|project-knowledge.md]] | プロジェクトナレッジの雛形 |
| [[_templates/agent-lesson\|agent-lesson.md]] | エージェント学習ログの雛形 |
