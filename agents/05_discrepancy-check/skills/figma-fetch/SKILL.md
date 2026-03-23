---
name: figma-fetch
description: Figma MCP を通じてデザイン上のテキストノードを取得するスキル。"Figma", "デザイン確認", "FigmaのURL", "figma.com" などのキーワードが出たら使用する。figma/urls.json に登録されたURLのみ対象とし、バス停名・路線名・行先名・時刻テキストをJSON形式で抽出する。
---

# Figma デザインデータ取得

Figma MCP (`get_design_context`) を使い、デザイン上のテキストデータを構造化JSONとして取得します。

## 前提条件

- `projects/<project-id>/figma/urls.json` にURLが登録済みであること
- Figma MCP サーバーが接続済みであること

## 作業フロー

### 1. URL一覧取得
`figma/urls.json` から対象エントリを読み込む。

```json
{
  "id": "chofu-001",
  "label": "1番のりば 京王バス 調布駅南口行き",
  "figmaUrl": "https://www.figma.com/design/...",
  "excelFile": "chofu-001.xlsx",
  "lastChecked": null
}
```

### 2. デザインデータ取得
Figma MCPの `get_design_context` でノード情報を取得する。
- `fileKey` と `nodeId` をURLからパースして使用
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
- Figma MCPが応答しない場合: `[ERROR]` メモリに記録してユーザーに報告
- `lastChecked` を取得成功時に更新する
