# AkaMaCorp

NFD（育成優先型開発）に基づくAIエージェント組織 + Obsidian ナレッジベース。

---

## エージェント構成

| # | エージェント | 役割 | 専門領域 |
|---|------------|------|---------|
| 01 | **secretary** | オーケストレーター | プロジェクト管理・エージェント管理・ルーティング・知識結晶化 |
| 02 | **reviewer** | レビュワー | PRレビュー・承認・マージ管理 |
| 03 | **obsidian** | 専門エージェント | Obsidian Vault管理・デイリーノート・ナレッジ記録・Bases/Canvas |
| 04 | designer | 専門エージェント | Web UI/UX / Figma / デザインシステム / Awwwards 基準 |
| 05 | sharepoint | 専門エージェント | SharePoint / SPFx / REST API / Power Automate / ScriptLoader |
| 06 | frontend | 専門エージェント | HTML / CSS / JavaScript / アクセシビリティ / QA |
| 07 | discrepancy-check | 専門エージェント | Excel / Figma 差分チェック・レポート生成 |

---

## フォルダ構成

```
AkaMaCorp/
├── agents/
│   ├── 01_secretary/                 ← 秘書エージェント（統括）
│   │   ├── CLAUDE.md
│   │   ├── agent-registry.md         ← 全エージェント登録情報
│   │   └── skills/
│   │       ├── plan-think/           ← THINK: 要求を問い直し PLAN.md 作成
│   │       ├── plan-review/          ← PLAN: アーキテクチャ・テスト計画・承認
│   │       ├── route-agent/          ← BUILD: エージェントへの振り分け
│   │       ├── project-track/        ← 全フェーズ: プロジェクト進捗管理
│   │       └── nfd-crystallize/      ← REFLECT: 知識結晶化サイクル管理
│   ├── 02_reviewer/                  ← レビュワーエージェント（PR管理）
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── review-pr/            ← PRレビュー・承認・差し戻し
│   │       └── merge-pr/             ← 承認済みPRのマージ
│   ├── 03_obsidian/                  ← Obsidianエージェント（Vault・ナレッジ管理）
│   │   ├── CLAUDE.md
│   │   └── skills/                   ← obsidian-skills プラグインを使用
│   ├── 04_designer/                  ← デザイン専門エージェント（Web UI/UX）
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── design-standards/     ← ベンチマーク基準・AI制作回避チェックリスト
│   │       ├── design-craft/         ← プロ品質UIテクニック集（タイポ・ボーダー等）
│   │       ├── wireframe/            ← ワイヤーフレーム・情報設計・プロトタイプ
│   │       ├── ux-review/            ← UX 評価・ヒューリスティック分析
│   │       ├── brand-guide/          ← デザイントークン・ブランドガイドライン
│   │       ├── design-handoff/       ← 開発向けデザイン仕様書作成
│   │       └── figma-design/         ← Figma MCP 連携・デザイン制作
│   ├── 05_sharepoint/                ← SharePoint 専門エージェント
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── spfx-component/       ← SPFx Web パーツ・拡張機能
│   │       ├── sp-rest-api/          ← SharePoint REST API / PnPjs
│   │       ├── xsp-script-loader/    ← JS/CSS 自動注入カスタマイズ
│   │       ├── ms-graph-api/         ← Microsoft Graph API
│   │       ├── power-automate-tips/  ← Power Automate フロー設計
│   │       ├── sp-permissions/       ← 権限・アクセス管理
│   │       └── sp-html-viewer/       ← sp-html-viewer 向け HTML
│   ├── 06_frontend/                  ← フロントエンド専門エージェント
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── ui-component/         ← 再利用可能コンポーネント
│   │       ├── css-layout/           ← レスポンシブレイアウト
│   │       ├── figma-to-code/        ← Figma → コード変換
│   │       ├── design-system/        ← デザイントークン標準化
│   │       ├── a11y-check/           ← WCAG アクセシビリティ
│   │       └── qa-check/             ← QA: 動作確認・バグ修正・リグレッションテスト
│   └── 07_discrepancy-check/         ← 差分チェック専門エージェント
│       ├── CLAUDE.md
│       └── skills/
│           ├── excel-parse/          ← ExcelデータをJSON抽出
│           ├── figma-fetch/          ← FigmaデザインデータをJSON取得
│           ├── diff-check/           ← ExcelとFigmaの照合・差分検出
│           └── report-gen/           ← 差分レポートMarkdown出力
├── memory/                           ← エクスペリエンス層（NFD）
│   ├── dashboard.md                  ← Dataviewナレッジダッシュボード
│   ├── daily/                        ← 日次ログ（YYYY-MM-DD.md）
│   ├── insights/                     ← 個別インサイトファイル
│   ├── errors/                       ← 個別エラーファイル
│   ├── crystallization/              ← 結晶化チェックポイント記録
│   └── templates/                    ← daily / insight / error テンプレート
├── projects/                         ← プロジェクト管理ファイル
├── CLAUDE.md                         ← Claude Code 常時コンテキスト
├── SPRINT-FLOW.md                    ← スプリントフロー定義
└── README.md
```

---

## スプリントフロー

詳細は `SPRINT-FLOW.md` を参照。

```
ユーザーの要求
    ↓
[THINK]   secretary: 要求を問い直し PLAN.md を作成
    ↓ ユーザー確認
[PLAN]    secretary: アーキテクチャ・テスト計画を確定
    ↓ ユーザー承認
[BUILD]   designer / sharepoint / frontend: デザイン・実装 → PR 作成
    ↓
[REVIEW]  reviewer: コードレビュー → 承認 or 差し戻し
    ↓ 承認
[QA]      frontend: 動作確認 → QA レポート
    ↓ 合格
[SHIP]    reviewer: マージ → secretary: ドキュメント更新
    ↓
[REFLECT] secretary + obsidian: 振り返り・知識結晶化
```

### タスク規模別の省略ルール

| 規模 | 必須フェーズ |
|------|------------|
| 小（バグ修正・1 ファイル） | BUILD → REVIEW → SHIP |
| 中（新機能・複数ファイル） | THINK → PLAN → BUILD → REVIEW → QA → SHIP |
| 大（新エージェント・設計変更） | 全フェーズ + ユーザー承認チェックポイント |

---

## エージェントの追加・変更ルール

エージェントを追加・変更したときは、**以下を必ずセットで更新する**:

1. `agents/<番号>_<エージェント名>/CLAUDE.md` を作成・編集
2. `agents/01_secretary/agent-registry.md` に登録・更新
3. `CLAUDE.md` のエージェント構成表を更新
4. **この `README.md` のエージェント構成表・フォルダ構成を更新** ← 必須

---

## スキルの更新

`skill-creator` スキルを使うことで、テスト・評価・改善のサイクルを回せます。

Obsidianエージェントは `obsidian-skills` プラグイン（obsidian-cli / obsidian-markdown / obsidian-bases / json-canvas / defuddle）と、AkaMaCorp Vault 専用の `obsidian:obsidian-agent` スキルを使用します。

---

## デザイン品質基準

04_designer は以下をベンチマークとして採用:

- **CSS Design Awards / Awwwards** — Web サイトのビジュアル・インタラクション基準
- **Apple HIG** — UI コンポーネント・空間デザイン基準
- **Google Material Design 3** — コンポーネント・カラーシステム基準

詳細は `agents/04_designer/skills/design-standards/SKILL.md` を参照。
