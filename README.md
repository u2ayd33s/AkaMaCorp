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
│   ├── secretary/                    ← 秘書エージェント（統括）
│   │   ├── CLAUDE.md
│   │   ├── agent-registry.md         ← 全エージェント登録情報
│   │   └── skills/
│   │       ├── route-agent/          ← エージェントへの振り分け
│   │       │   └── SKILL.md
│   │       └── project-track/        ← プロジェクト進捗管理
│   │           └── SKILL.md
│   ├── reviewer/                     ← レビュワーエージェント（PR管理）
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── review-pr/            ← PRレビュー・承認・差し戻し
│   │       │   └── SKILL.md
│   │       └── merge-pr/             ← 承認済みPRのマージ
│   │           └── SKILL.md
│   ├── sharepoint/                   ← SharePoint 専門エージェント
│   │   ├── CLAUDE.md
│   │   └── skills/
│   │       ├── spfx-component/
│   │       └── sp-rest-api/
│   └── frontend/                     ← フロントエンド専門エージェント
│       ├── CLAUDE.md
│       └── skills/
│           ├── ui-component/
│           └── css-layout/
├── projects/                         ← プロジェクト管理ファイル（随時生成）
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
