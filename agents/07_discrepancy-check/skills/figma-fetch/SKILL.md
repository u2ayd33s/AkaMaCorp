---
name: figma-fetch
description: Figma MCP を通じてデザイン上のテキストノードを取得するスキル。"Figma", "デザイン確認", "FigmaのURL", "figma.com" などのキーワードが出たら使用する。figma/urls.json に登録されたURLのみ対象とし、バス停名・路線名・行先名・時刻テキストをJSON形式で抽出する。
---

# Figma デザインデータ取得

Figma MCP (`get_metadata`) を使い、デザイン上のテキストデータを構造化JSONとして取得します。

> **注意**: `get_design_context` は UI実装用ツールのため使用しない。データ抽出には `get_metadata` を使用する。

## 前提条件

- `projects/<project-id>/figma/urls.json` にURLが登録済みであること
- Figma MCP サーバーが接続済みであること

## タイムアウト

- 1ノードあたり **2分以内** に応答がない場合はエラー扱いとし、処理を中断してユーザーに報告する
- 2分を超えた場合: `[ERROR]` メモリに記録 → Issue にコメント → `⏳ status: waiting` に戻す

## 作業フロー

### 1. URL一覧取得
`figma/urls.json` から対象シートのエントリを読み込む。

```json
{
  "figmaSheetName": "timetable_weekdays_south_02",
  "nodeId": "3504-10580",
  "figmaUrl": "https://www.figma.com/design/..."
}
```

### 2. デザインデータ取得（平日・土曜・祝日を並列取得）

同一シートの平日・土曜・祝日の3ノードは **並列で同時取得** する。

```
// 並列取得（逐次ではなく同時に呼び出す）
get_metadata(fileKey, nodeId_weekday)
get_metadata(fileKey, nodeId_saturday)
get_metadata(fileKey, nodeId_holiday)
```

- `fileKey` と `nodeId` は `figma/urls.json` の値をそのまま使用
- テキストノードのみ抽出対象

### 3. JSON出力形式

```json
{
  "source": "figma",
  "figmaUrl": "https://www.figma.com/design/...",
  "fetchedAt": "2026-03-24T00:00:00Z",
  "data": [
    {
      "nodeId": "123:456",
      "nodeName": "BusStop/Name",
      "textContent": "Chofu Station South Exit",
      "category": "busStopEn"
    }
  ]
}
```

## カテゴリ分類ルール

| ノード名パターン | カテゴリ |
|----------------|---------|
| `BusStop/*` | `busStop` / `busStopEn` |
| `Route/*` | `route` |
| `Destination/*` | `destination` / `destinationEn` |
| `Time/*` | `times` |

## エラーハンドリング

- URLが `figma/urls.json` に未登録の場合: 処理を中断し確認を求める
- Figma MCP が **2分以内** に応答しない場合: `[ERROR]` メモリに記録してユーザーに報告
- `lastChecked` を取得成功時に更新する
