---
name: diff-check
description: ExcelデータとFigmaデータを照合して差分を検出するスキル。"差分チェック", "照合", "不一致", "ズレ", "比較" などのキーワードが出たら使用する。excel-parse と figma-fetch の出力JSONを入力とし、欠損・余剰・不一致を分類した差分リストを生成する。
---

# 差分チェック実行

ExcelデータとFigmaデータを照合し、差分を分類・検出します。

## 入力

- `excel-parse` の出力JSON（Excelデータ）
- `figma-fetch` の出力JSON（Figmaデータ）

## 差分分類

| 種別 | 説明 | 重大度 |
|------|------|--------|
| `MISMATCH` | ExcelとFigmaで値が異なる | 🔴 HIGH |
| `MISSING_IN_FIGMA` | Excelにあるがデザインに存在しない | 🟠 MEDIUM |
| `EXTRA_IN_FIGMA` | デザインにあるがExcelに存在しない | 🟡 LOW |
| `FORMAT_ERROR` | 時刻フォーマット不正（HH:MM形式でない等） | 🔴 HIGH |

## 差分リスト出力形式

```json
{
  "checkedAt": "2026-03-24T00:00:00Z",
  "projectId": "chofu-001",
  "totalExcelItems": 42,
  "totalFigmaItems": 40,
  "diffCount": 3,
  "diffs": [
    {
      "type": "MISMATCH",
      "severity": "HIGH",
      "field": "destinationEn",
      "excelValue": "Shinjuku Station",
      "figmaValue": "Shinjuku Sta.",
      "nodeId": "123:456"
    },
    {
      "type": "MISSING_IN_FIGMA",
      "severity": "MEDIUM",
      "field": "times",
      "excelValue": "23:30",
      "figmaValue": null,
      "nodeId": null
    }
  ]
}
```

## 照合ロジック

1. `id`（プロジェクトID）でエントリを対応付け
2. 各フィールドを文字列正規化（全角→半角、空白トリム）してから比較
3. 時刻は `HH:MM` 形式に統一してから比較
4. 英語テキストは大文字・小文字を区別する

## 文字列正規化ルール

- 全角英数字 → 半角英数字
- 前後の空白・改行を除去
- 中黒（・）→ スラッシュ（/）に統一
