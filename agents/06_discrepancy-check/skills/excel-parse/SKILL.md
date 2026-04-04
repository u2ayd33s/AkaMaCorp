---
name: excel-parse
description: ExcelファイルからバスデータをJSON形式で抽出するスキル。"Excel", "時刻表", "バス停", "路線名", "行先", "xlsm", "xlsx" などのキーワードが出たら使用する。openpyxl または xlsx-parser を使い、バス停名・路線名・行先名・運行時刻を構造化JSONとして出力する。
---

# Excel データ解析

時刻表Excelファイルから差分チェック用データを抽出します。

## 対応フォーマット

- `.xlsx` / `.xlsm` 形式
- 対象ディレクトリ: `projects/<project-id>/data/excel/`

## 作業フロー

### 1. 設定確認
`config.json` の `excelMapping` でシート名・列構成を確認する。

### 2. データ抽出

抽出対象フィールド:
| フィールド | 説明 |
|-----------|------|
| `busStop` | バス停名（日本語・英語） |
| `route` | 路線名 |
| `destination` | 行先名（日本語・英語） |
| `times` | 運行時刻リスト（HH:MM形式） |

### 3. JSON出力形式

```json
{
  "source": "excel",
  "file": "chofu-001.xlsx",
  "extractedAt": "2026-03-24T00:00:00Z",
  "data": [
    {
      "busStop": "調布駅南口",
      "busStopEn": "Chofu Station South Exit",
      "route": "京王バス",
      "destination": "新宿駅",
      "destinationEn": "Shinjuku Station",
      "times": ["07:00", "07:30", "08:00"]
    }
  ]
}
```

## エラーハンドリング

- シートが見つからない場合: `config.json` の `excelMapping.sheetName` を確認
- 列が一致しない場合: フォーマット変更として `[ERROR]` メモリに記録
- 空セルは `null` として扱い、差分チェック時に欠損として報告
