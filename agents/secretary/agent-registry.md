# エージェントレジストリ

秘書エージェントが管理する全エージェントと、そのスキル一覧。
新しいエージェントを追加したらここを必ず更新してください。

---

## 登録エージェント

### sharepoint
- **パス**: `agents/sharepoint/`
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
- **パス**: `agents/frontend/`
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
- **向いているタスク例**:
  - ページのレイアウトを作りたい
  - モーダルやドロップダウンなど UI パーツが欲しい
  - モバイル対応のデザインを実装したい
  - Figma のデザインをそのままコードにしたい
  - 会社のデザインシステム・スタイルガイドを作りたい
  - アクセシビリティに問題がないか確認したい

---

### reviewer
- **パス**: `agents/reviewer/`
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

## エージェント追加手順

1. `agents/<エージェント名>/` ディレクトリを作成
2. `CLAUDE.md` にエージェントの役割・スキルを定義
3. `skills/<スキル名>/SKILL.md` を作成
4. このファイル (`agent-registry.md`) に追記
5. `README.md` のエージェント構成表を更新
