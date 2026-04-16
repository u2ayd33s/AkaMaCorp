---
project: chofu-station-signage
tags: [knowledge, chofu-station-signage, figma, structure]
created: 2026-04-16
updated: 2026-04-16
---

# 調布駅バス時刻表サイネージ — Figmaノード構造

## ノード階層

```
フレームノード（例: 3504:13349）
└── timetable [FRAME]
    ├── <instance> [INSTANCE]  # 5時（index 0）
    ├── <instance> [INSTANCE]  # 6時（index 1）
    ├── ...
    └── <instance> [INSTANCE]  # 23時（index 18）  ← 計19個
        └── #time1, #time2, ... #timeN [TEXT]
```

## 時刻スロットのマッピング

各 instance 内の `#timeN` は Excelの列順に対応:

```
time1  〜 time(n1)           → 平日 枠1〜枠n1
time(n1+1) 〜 time(n1+n2)   → 土曜 枠1〜枠n2
time(n1+n2+1) 〜 time(N)    → 日祝 枠1〜枠n3
```

n1/n2/n3 は `config.json` の `frameCount` から取得（CSVヘッダーからも動的取得可）。

## 「便なし」の扱い

| 表現 | 意味 | Excelとの照合 |
|------|------|-------------|
| `''`（空文字） | 便なし | Excel `None` と一致扱い |
| `'*'`（プレースホルダー） | 便なし | Excel `None` と一致扱い |

## 行先ノードの注意点

- `check_meta`（行先差分チェック）は **平日の `行先` 行のみ** と Figma `行先①②...` を比較
- Figmaでノード名 == テキスト内容（例: `行先②` → `"行先②"`）のノードは**ラベルなので除外**

## シート別形式の違い

| シート | 形式 | 備考 |
|--------|------|------|
| 南1 | 旧形式 | 平日/土曜/日祝 が**別ノード** |
| 南2〜北15 | 新形式 | 1ノードに**全スケジュール統合** |

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（WORKFLOW.md から移植） |
