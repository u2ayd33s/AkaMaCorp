---
name: sp-rest-api
description: SharePoint REST API および Microsoft Graph API を使ったコードを生成するスキル。"SharePoint REST", "リスト取得", "アイテム作成", "Graph API", "CAML クエリ", "OData フィルター", "認証", "アクセストークン" などのキーワードが出たら積極的に使用する。CRUD 操作・フィルタリング・バッチ処理・認証フローまでカバーし、エラーハンドリング込みの実装コードを生成する。
---

# SharePoint REST API 開発

SharePoint REST API / Microsoft Graph API の呼び出しコードを生成します。

## 対応範囲

- **リスト・ライブラリ操作**: CRUD、フィルタリング、ソート、ページング
- **ユーザー・グループ管理**
- **サイト・サブサイト操作**
- **Microsoft Graph API**: メール、Teams、OneDrive 等
- **バッチリクエスト**: 複数操作をまとめて送信
- **認証**: SPFx context / MSAL / PnPjs

## エンドポイント早見表

| 操作 | エンドポイント |
|------|--------------|
| リスト一覧取得 | `/_api/web/lists` |
| リストアイテム取得 | `/_api/web/lists/getbytitle('リスト名')/items` |
| アイテム作成 | `/_api/web/lists/getbytitle('リスト名')/items` (POST) |
| アイテム更新 | `/_api/web/lists/getbytitle('リスト名')/items(ID)` (MERGE) |
| アイテム削除 | `/_api/web/lists/getbytitle('リスト名')/items(ID)` (DELETE) |

## 実装パターン

### 基本的な GET リクエスト (Fetch API)
```typescript
async function getListItems(siteUrl: string, listName: string): Promise<any[]> {
  const endpoint = `${siteUrl}/_api/web/lists/getbytitle('${listName}')/items`;

  try {
    const response = await fetch(endpoint, {
      headers: {
        'Accept': 'application/json;odata=verbose',
        'Content-Type': 'application/json;odata=verbose'
      }
    });
    if (!response.ok) throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    const data = await response.json();
    return data.d.results;
  } catch (error) {
    console.error('リストアイテム取得エラー:', error);
    throw error;
  }
}
```

### PnPjs を使ったシンプルな実装 (推奨)
```typescript
import { spfi, SPFx } from '@pnp/sp';
import '@pnp/sp/webs';
import '@pnp/sp/lists';
import '@pnp/sp/items';

const sp = spfi().using(SPFx(this.context));

// アイテム取得 (フィルター・選択カラム付き)
const items = await sp.web.lists
  .getByTitle('タスク一覧')
  .items
  .filter("Status eq '進行中'")
  .select('Title', 'AssignedTo', 'DueDate')
  .top(100)();

// アイテム作成
await sp.web.lists.getByTitle('タスク一覧').items.add({
  Title: '新しいタスク',
  Status: '未着手'
});

// アイテム更新
await sp.web.lists.getByTitle('タスク一覧').items.getById(1).update({
  Status: '完了'
});
```

### Request Digest (POST/PATCH/DELETE に必要)
```typescript
async function getRequestDigest(siteUrl: string): Promise<string> {
  const response = await fetch(`${siteUrl}/_api/contextinfo`, {
    method: 'POST',
    headers: { 'Accept': 'application/json;odata=verbose' }
  });
  const data = await response.json();
  return data.d.GetContextWebInformation.FormDigestValue;
}
```

## OData フィルター記法

```
// 文字列一致
$filter=Title eq '値'

// 数値比較
$filter=ID gt 10

// 日付フィルター
$filter=Created ge '2024-01-01T00:00:00Z'

// AND / OR
$filter=Status eq '進行中' and AssignedTo eq 'user@example.com'

// contains (部分一致)
$filter=substringof('キーワード', Title)
```

## ベストプラクティス

- **PnPjs 優先**: 生の Fetch より PnPjs を使う (型安全・エラー処理が充実)
- **Select で必要カラムのみ取得**: `$select=Title,ID` でパフォーマンス改善
- **Top でページング**: デフォルト 100 件制限に注意、`$top` と `$skiptoken` で制御
- **エラーハンドリング**: HTTP ステータスコードごとに適切なメッセージを表示

## 更新履歴
- v1.0: 初版作成
