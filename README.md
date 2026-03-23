# AkaMaCorp

## Issues ラベル管理

Issues での依頼・管理に使用するラベル体系です。新しいラベルを追加した際はこのセクションも更新してください。

### 依頼者 (`from:`)

| ラベル | 色 | 説明 |
|--------|----|------|
| `from: user` | ![\#0052CC](https://placehold.co/12x12/0052CC/0052CC.png) `#0052CC` | ユーザーからの依頼 |
| `from: claude-code` | ![\#5319E7](https://placehold.co/12x12/5319E7/5319E7.png) `#5319E7` | ClaudeCode からの依頼・提案 |

### 作業種別 (`type:`)

| ラベル | 色 | 説明 |
|--------|----|------|
| `type: check` | ![\#FBCA04](https://placehold.co/12x12/FBCA04/FBCA04.png) `#FBCA04` | 差分チェック・確認作業 |
| `type: register` | ![\#0E8A16](https://placehold.co/12x12/0E8A16/0E8A16.png) `#0E8A16` | URL・データ登録 |
| `type: fix` | ![\#E11D48](https://placehold.co/12x12/E11D48/E11D48.png) `#E11D48` | 修正依頼 |
| `type: setup` | ![\#006B75](https://placehold.co/12x12/006B75/006B75.png) `#006B75` | セットアップ・初期設定 |
| `type: report` | ![\#1D76DB](https://placehold.co/12x12/1D76DB/1D76DB.png) `#1D76DB` | レポート出力・集計 |

### ステータス (`status:`)

| ラベル | 色 | 説明 |
|--------|----|------|
| `status: in-progress` | ![\#FF7619](https://placehold.co/12x12/FF7619/FF7619.png) `#FF7619` | 対応中 |
| `status: done` | ![\#2EA44F](https://placehold.co/12x12/2EA44F/2EA44F.png) `#2EA44F` | 完了 |
| `status: waiting` | ![\#BFD4F2](https://placehold.co/12x12/BFD4F2/BFD4F2.png) `#BFD4F2` | 情報待ち・保留中 |

---


## エージェント構成

| エージェント | 役割 | 専門領域 |
|------------|------|---------|
| **secretary** | オーケストレーター | プロジェクト管理・エージェント管理・ルーティング |
| **reviewer** | レビュワー | PRレビュー・承認・マージ管理 |
| sharepoint | 専門エージェント | SharePoint / SPFx / REST API |
| frontend | 専門エージェント | HTML / CSS / JavaScript / UI/UX |

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
│   ├── 03_sharepoint/                ← SharePoint 専門エージェント
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── spfx-component/       ← SPFx Web パーツ・拡張機能
│   │       ├── sp-rest-api/          ← SharePoint REST API / PnPjs
│   │       ├── xsp-script-loader/    ← JS/CSS 自動注入カスタマイズ
│   │       ├── ms-graph-api/         ← Microsoft Graph API
│   │       ├── power-automate-tips/  ← Power Automate フロー設計
│   │       ├── sp-permissions/       ← 権限・アクセス管理
│   │       └── sp-html-viewer/       ← sp-html-viewer 向け HTML
│   └── 04_frontend/                  ← フロントエンド専門エージェント
│       ├── CLAUDE.md
│       └── skills/
│           ├── ui-component/         ← 再利用可能コンポーネント
│           ├── css-layout/           ← レスポンシブレイアウト
│           ├── figma-to-code/        ← Figma → コード変換
│           ├── design-system/        ← デザイントークン標準化
│           ├── a11y-check/           ← WCAG アクセシビリティ
│           └── qa-check/             ← QA: 動作確認・バグ修正・リグレッションテスト
├── memory/                           ← エクスペリエンス層（NFD）
│   ├── README.md                     ← 使い方説明
│   ├── insights.md                   ← 洞察・発見ログ
│   ├── error-patterns.md             ← エラー・失敗パターン
│   ├── daily/                        ← 日次ログ（YYYY-MM-DD.md）
│   └── crystallization/              ← 結晶化チェックポイント記録
├── projects/                         ← プロジェクト管理ファイル
├── SPRINT-FLOW.md                    ← スプリントフロー定義（gstack準拠）
└── README.md
```

## スプリントフロー

詳細は `SPRINT-FLOW.md` を参照。

```
ユーザーの要求
    ↓
[THINK]   secretary: 要求を問い直し PLAN.md を作成
    ↓ ユーザー確認
[PLAN]    secretary: アーキテクチャ・テスト計画を確定
    ↓ ユーザー承認
[BUILD]   sharepoint / frontend: 実装 → PR 作成
    ↓
[REVIEW]  reviewer: コードレビュー → 承認 or 差し戻し
    ↓ 承認
[QA]      frontend: 動作確認 → QA レポート
    ↓ 合格
[SHIP]    reviewer: マージ → secretary: ドキュメント更新
    ↓
[REFLECT] secretary: 振り返り・知識結晶化
```

### タスク規模別の省略ルール
| 規模 | 必須フェーズ |
|------|------------|
| 小（バグ修正・1 ファイル） | BUILD → REVIEW → SHIP |
| 中（新機能・複数ファイル） | THINK → PLAN → BUILD → REVIEW → QA → SHIP |
| 大（新エージェント・設計変更） | 全フェーズ + ユーザー承認チェックポイント |

### エージェントの追加
1. `agents/<エージェント名>/` にディレクトリを作成
2. `CLAUDE.md` と `skills/<スキル名>/SKILL.md` を作成
3. `agents/secretary/agent-registry.md` に登録

## スキルの更新
skill-creator スキルを使うことで、テスト・評価・改善のサイクルを回せます。
