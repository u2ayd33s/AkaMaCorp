# エージェントレジストリ

秘書エージェントが管理する全エージェントと、そのスキル一覧。
新しいエージェントを追加したらここを必ず更新してください。

---

## 登録エージェント

### secretary
- **パス**: `agents/01_secretary/`
- **専門領域**: オーケストレーション・企画立案・実装計画・プロジェクト管理・知識結晶化
- **スキル**:
  | スキル | 概要 | スプリントフェーズ |
  |--------|------|----------------|
  | `plan-think` | 要求を問い直し PLAN.md を作成 | THINK |
  | `plan-review` | アーキテクチャ・テスト計画の確定とユーザー承認 | PLAN |
  | `route-agent` | ユーザー指示を解析して最適なエージェントへ割り振り | BUILD |
  | `project-track` | プロジェクト・タスクの進捗管理と状況レポート | 全フェーズ |
  | `nfd-crystallize` | エクスペリエンス層の管理・知識結晶化サイクルの実行 | REFLECT |

---

### sharepoint
- **パス**: `agents/03_sharepoint/`
- **専門領域**: SharePoint Online / SPFx / REST API / Microsoft Graph / Power Automate / 権限管理 / sp-html-viewer
- **得意なこと**: SPFx Web パーツ・拡張機能の開発、SharePoint REST API 実装、Graph API 活用、Power Automate フロー設計、権限・アクセス管理、sp-html-viewer 向け HTML 作成
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `spfx-component` | SPFx Web パーツ・拡張機能の雛形生成と実装 |
  | `sp-rest-api` | SharePoint REST API / PnPjs コード生成 |
  | `xsp-script-loader` | ページパスに対応した JS/CSS 自動注入によるカスタマイズ開発 |
  | `ms-graph-api` | Microsoft Graph API（ユーザー・Teams・カレンダー・OneDrive）実装 |
  | `power-automate-tips` | Power Automate 連携フロー設計パターン（承認・通知・バッチ） |
  | `sp-permissions` | サイト権限・共有リンク・アクセス管理の設計と REST/Graph 実装 |
  | `sp-html-viewer` | sp-html-viewer Web パーツ向け HTML/CSS/JS の作成 |
- **向いているタスク例**:
  - SharePoint リストのデータ取得・更新処理を作りたい
  - SPFx で社内ポータルのコンポーネントを開発したい
  - Graph API でユーザー・Teams・カレンダー情報を取得したい
  - SharePoint の特定ページにカスタムデザインや機能を追加したい
  - SPFx を使わずにページをカスタマイズしたい（ScriptLoader 経由）
  - Power Automate で承認フロー・Teams 通知を自動化したい
  - サイトの権限設計・共有リンク管理を整理したい
  - sp-html-viewer で表示する HTML ページを作りたい

---

### frontend
- **パス**: `agents/04_frontend/`
- **専門領域**: HTML / CSS / JavaScript / UI/UX / レスポンシブデザイン / Figma / デザインシステム / アクセシビリティ
- **得意なこと**: UI コンポーネント生成、CSS レイアウト設計、Figma デザインのコード化、デザイントークン設計、WCAG 対応
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `ui-component` | モーダル・タブ・フォームなど再利用可能コンポーネント生成 |
  | `css-layout` | Grid/Flex を使ったレスポンシブレイアウト設計 |
  | `figma-to-code` | Figma デザインから HTML/CSS/JS への変換（Figma MCP 連携） |
  | `design-system` | カラー・タイポグラフィ・スペーシングなどデザイントークンの標準化 |
  | `a11y-check` | WCAG 2.1 AA 準拠チェックと修正提案 |
  | `qa-check` | 動作確認・バグ修正・リグレッションテスト生成 |
- **向いているタスク例**:
  - ページのレイアウトを作りたい
  - モーダルやドロップダウンなど UI パーツが欲しい
  - モバイル対応のデザインを実装したい
  - Figma のデザインをそのままコードにしたい
  - 会社のデザインシステム・スタイルガイドを作りたい
  - アクセシビリティに問題がないか確認したい

---

### reviewer
- **パス**: `agents/02_reviewer/`
- **専門領域**: PRレビュー・検証・マージ管理
- **得意なこと**: 各エージェントのPR差分確認、エージェント別レビュー基準の適用、承認・マージ・差し戻し判断
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `review-pr` | PRの内容確認・検証・承認または差し戻しコメント |
  | `merge-pr` | 承認済みPRのマージとローカル同期 |
- **マージ権限リポジトリ**: `AkaMaCorp`, `sharepoint-scriptloader`
- **向いているタスク例**:
  - エージェントが作成したPRをレビューしたい
  - PRに問題がないか確認してからマージしたい
  - 差し戻しが必要な場合にコメントを残したい

---

### discrepancy-check
- **パス**: `agents/05_discrepancy-check/`
- **専門領域**: Excel / Figma / 差分チェック / レポート生成
- **得意なこと**: 時刻表Excelデータの解析、FigmaデザインからのテキストデータJSON取得、ExcelとFigmaの照合・差分検出、Markdownレポート自動生成
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `excel-parse` | ExcelファイルからバスデータをJSON形式で抽出 |
  | `figma-fetch` | Figma MCPを通じてデザイン上のテキストノードをJSON取得 |
  | `diff-check` | ExcelデータとFigmaデータを照合し差分リストを生成 |
  | `report-gen` | 差分リストをMarkdownレポートに変換して reports/ へ出力 |
- **向いているタスク例**:
  - ExcelとFigmaの時刻表データに差分がないか確認したい
  - バス停名・路線名・行先名の英語表記がデザインと合っているか検証したい
  - 差分チェック結果をレポートとして出力したい
  - 定期的に時刻表データの整合性を確認したい

---

## エージェント追加手順

1. `agents/<エージェント名>/` ディレクトリを作成
2. `CLAUDE.md` にエージェントの役割・スキルを定義
3. `skills/<スキル名>/SKILL.md` を作成
4. このファイル (`agent-registry.md`) に追記
5. `README.md` のエージェント構成表を更新
