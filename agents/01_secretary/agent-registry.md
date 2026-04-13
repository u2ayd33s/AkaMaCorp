---
tags: [registry, agents]
---

# エージェントレジストリ

秘書エージェントが管理する全エージェントと、そのスキル一覧。
新しいエージェントを追加したらここを必ず更新してください。

---

## 登録エージェント

### [[01_secretary/CLAUDE|secretary]]
- **専門領域**: オーケストレーション・企画立案・実装計画・プロジェクト管理・知識結晶化
- **スキル**:
  | スキル | 概要 | スプリントフェーズ |
  |--------|------|----------------|
  | [[plan-think/SKILL\|plan-think]] | 要求を問い直し PLAN.md を作成 | THINK |
  | [[plan-review/SKILL\|plan-review]] | アーキテクチャ・テスト計画の確定とユーザー承認 | PLAN |
  | [[route-agent/SKILL\|route-agent]] | ユーザー指示を解析して最適なエージェントへ割り振り | BUILD |
  | [[project-track/SKILL\|project-track]] | プロジェクト・タスクの進捗管理と状況レポート | 全フェーズ |
  | [[nfd-crystallize/SKILL\|nfd-crystallize]] | エクスペリエンス層の管理・知識結晶化サイクルの実行 | REFLECT |

---

### [[02_reviewer/CLAUDE|reviewer]]
- **専門領域**: PRレビュー・検証・マージ管理
- **得意なこと**: 各エージェントのPR差分確認、エージェント別レビュー基準の適用、承認・マージ・差し戻し判断
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | [[review-pr/SKILL\|review-pr]] | PRの内容確認・検証・承認または差し戻しコメント |
  | [[merge-pr/SKILL\|merge-pr]] | 承認済みPRのマージとローカル同期 |
- **マージ権限リポジトリ**: `AkaMaCorp`, `sharepoint-scriptloader`

---

### [[03_obsidian/CLAUDE|obsidian]]
- **専門領域**: Obsidian Vault 管理 / デイリーノート / ナレッジ記録 / Bases / Canvas / Webコンテンツ取込
- **得意なこと**: AkaMaCorp Vault へのノート作成・追記・検索、DECISION/INSIGHT/ERROR の記録、`.base` / `.canvas` ファイル作成、セッション終了時のルーティン実行
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `obsidian:obsidian-agent` | Vault全体の操作・記録オーケストレーション（AkaMaCorp専用） |
  | `obsidian:obsidian-cli` | ノート作成・読込・追記・検索（Obsidian CLIコマンド） |
  | `obsidian:obsidian-markdown` | Obsidian記法（wikilink・callout・frontmatter）での正確な記述 |
  | `obsidian:obsidian-bases` | `.base` ファイルでデータベースビューを構築 |
  | `obsidian:json-canvas` | `.canvas` ファイルでマインドマップ・フロー図を作成 |
  | `obsidian:defuddle` | Webページをクリーンなmarkdownに変換してVaultへ保存 |

---

### [[04_designer/CLAUDE|designer]]
- **専門領域**: Web UI/UX デザイン / Figma / ワイヤーフレーム / デザインシステム / ブランドガイドライン / デザインハンドオフ
- **得意なこと**: ワイヤーフレーム・プロトタイプ設計、Figma デザイン制作・管理、UX 評価・改善提案、デザイントークン体系化、開発向けデザイン仕様書作成
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | [[design-standards/SKILL\|design-standards]] | CSS Design Awards / Awwwards / Apple HIG / Material 基準・AI制作回避チェックリスト |
  | [[design-craft/SKILL\|design-craft]] | プロ品質UIテクニック（Inter Variable・半透明リング・ネスト角丸・非対称レイアウト等） |
  | [[wireframe/SKILL\|wireframe]] | ワイヤーフレーム・情報設計・プロトタイプ設計 |
  | [[ux-review/SKILL\|ux-review]] | UX 評価・ヒューリスティック分析・実装乖離チェック |
  | [[brand-guide/SKILL\|brand-guide]] | カラー・タイポグラフィ等デザイントークンの体系化 |
  | [[design-handoff/SKILL\|design-handoff]] | 開発向けデザイン仕様書・コンポーネント仕様の作成 |
  | [[figma-design/SKILL\|figma-design]] | Figma MCP を活用したデザイン制作・取得・管理 |

---

### [[05_sharepoint/CLAUDE|sharepoint]]
- **専門領域**: SharePoint Online / SPFx / REST API / Microsoft Graph / Power Automate / 権限管理 / sp-html-viewer
- **得意なこと**: SPFx Web パーツ・拡張機能の開発、SharePoint REST API 実装、Graph API 活用、Power Automate フロー設計、権限・アクセス管理、sp-html-viewer 向け HTML 作成
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | [[spfx-component/SKILL\|spfx-component]] | SPFx Web パーツ・拡張機能の雛形生成と実装 |
  | [[sp-rest-api/SKILL\|sp-rest-api]] | SharePoint REST API / PnPjs コード生成 |
  | [[xsp-script-loader/SKILL\|xsp-script-loader]] | ページパスに対応した JS/CSS 自動注入によるカスタマイズ開発 |
  | [[ms-graph-api/SKILL\|ms-graph-api]] | Microsoft Graph API（ユーザー・Teams・カレンダー・OneDrive）実装 |
  | [[power-automate-tips/SKILL\|power-automate-tips]] | Power Automate 連携フロー設計パターン（承認・通知・バッチ） |
  | [[sp-permissions/SKILL\|sp-permissions]] | サイト権限・共有リンク・アクセス管理の設計と REST/Graph 実装 |
  | [[sp-html-viewer/SKILL\|sp-html-viewer]] | sp-html-viewer Web パーツ向け HTML/CSS/JS の作成 |

---

### [[06_frontend/CLAUDE|frontend]]
- **専門領域**: HTML / CSS / JavaScript / UI/UX / レスポンシブデザイン / Figma / デザインシステム / アクセシビリティ
- **得意なこと**: UI コンポーネント生成、CSS レイアウト設計、Figma デザインのコード化、デザイントークン設計、WCAG 対応
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | [[ui-component/SKILL\|ui-component]] | モーダル・タブ・フォームなど再利用可能コンポーネント生成 |
  | [[css-layout/SKILL\|css-layout]] | Grid/Flex を使ったレスポンシブレイアウト設計 |
  | [[figma-to-code/SKILL\|figma-to-code]] | Figma デザインから HTML/CSS/JS への変換（Figma MCP 連携） |
  | [[design-system/SKILL\|design-system]] | カラー・タイポグラフィ・スペーシングなどデザイントークンの標準化 |
  | [[a11y-check/SKILL\|a11y-check]] | WCAG 2.1 AA 準拠チェックと修正提案 |
  | [[qa-check/SKILL\|qa-check]] | 動作確認・バグ修正・リグレッションテスト生成 |

---

### [[07_discrepancy-check/CLAUDE|discrepancy-check]]
- **専門領域**: Excel / Figma / 差分チェック / レポート生成
- **得意なこと**: 時刻表Excelデータの解析、FigmaデザインからのテキストデータJSON取得、ExcelとFigmaの照合・差分検出、Markdownレポート自動生成
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | [[excel-parse/SKILL\|excel-parse]] | ExcelファイルからバスデータをJSON形式で抽出 |
  | [[figma-fetch/SKILL\|figma-fetch]] | Figma MCPを通じてデザイン上のテキストノードをJSON取得 |
  | [[diff-check/SKILL\|diff-check]] | ExcelデータとFigmaデータを照合し差分リストを生成 |
  | [[report-gen/SKILL\|report-gen]] | 差分リストをMarkdownレポートに変換して reports/ へ出力 |

---

## エージェント追加手順

1. `agents/<エージェント名>/` ディレクトリを作成
2. `CLAUDE.md` にエージェントの役割・スキルを定義
3. `skills/<スキル名>/SKILL.md` を作成
4. このファイル (`agent-registry.md`) に追記
5. [[CLAUDE|Vault CLAUDE.md]] のエージェント構成表を更新
