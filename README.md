# AkaMaCorp

## エージェント構成

| エージェント | 専門領域 | ディレクトリ |
|------------|---------|------------|
| SharePoint Agent | SharePoint / SPFx / REST API | `agents/sharepoint/` |
| Frontend Agent | HTML / CSS / JS / UI/UX | `agents/frontend/` |

## フォルダ構成

```
AkaMaCorp/
├── agents/
│   ├── sharepoint/
│   │   ├── CLAUDE.md          # エージェント定義
│   │   └── skills/
│   │       ├── spfx-component/ # SPFx 開発スキル
│   │       │   └── SKILL.md
│   │       └── sp-rest-api/    # SharePoint REST API スキル
│   │           └── SKILL.md
│   └── frontend/
│       ├── CLAUDE.md          # エージェント定義
│       └── skills/
│           ├── ui-component/  # UI コンポーネント生成スキル
│           │   └── SKILL.md
│           └── css-layout/    # CSS レイアウト設計スキル
│               └── SKILL.md
└── README.md
```

## スキルの更新方法

各スキルの `SKILL.md` を直接編集してください。
skill-creator スキルを使うことで、テスト・評価・改善のサイクルを回せます。
