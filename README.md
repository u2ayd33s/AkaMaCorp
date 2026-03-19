# AkaMaCorp

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
│   │       ├── route-agent/          ← エージェントへの振り分け
│   │       ├── project-track/        ← プロジェクト進捗管理
│   │       └── nfd-crystallize/      ← 知識結晶化サイクル管理
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
│           └── a11y-check/           ← WCAG アクセシビリティ
├── memory/                           ← エクスペリエンス層（NFD）
│   ├── README.md                     ← 使い方説明
│   ├── insights.md                   ← 洞察・発見ログ
│   ├── error-patterns.md             ← エラー・失敗パターン
│   ├── daily/                        ← 日次ログ（YYYY-MM-DD.md）
│   └── crystallization/              ← 結晶化チェックポイント記録
├── projects/                         ← プロジェクト管理ファイル
└── README.md
```

## エージェントの使い方

### 基本フロー
```
ユーザーの指示
    ↓
secretary が内容を解析（route-agent スキル）
    ↓
適切な専門エージェントへ割り振り
    ↓
実行結果を secretary が管理・記録（project-track スキル）
```

### エージェントの追加
1. `agents/<エージェント名>/` にディレクトリを作成
2. `CLAUDE.md` と `skills/<スキル名>/SKILL.md` を作成
3. `agents/secretary/agent-registry.md` に登録

## スキルの更新
skill-creator スキルを使うことで、テスト・評価・改善のサイクルを回せます。
