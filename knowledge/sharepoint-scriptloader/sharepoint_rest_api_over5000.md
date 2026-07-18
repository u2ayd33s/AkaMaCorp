# SharePoint REST API — 5,000件超えデータ取得 完全ガイド

> **対象読者:** SharePoint 開発者 / Power Platform 開発者  
> **最終更新:** 2025年

---

## なぜ5,000件が壁になるのか

SharePoint には **List View Threshold（リストビューしきい値）** が設定されており、デフォルトで **5,000件** を超えるクエリはブロックされます。

| 設定項目 | 既定値 |
|---|---|
| List View Threshold | 5,000 件 |
| 管理者による上限（メンテナンス時間帯） | 20,000 件 |
| Search API での取得上限 | 制限なし（ページング必要） |

この制限は **インデックスが存在しない列に対するフィルター** や **ソート** 時に発動します。インデックス済み列を使うか、以下の手法で回避します。

---

## 手法一覧

| # | 手法 | エンドポイント | 5,000件制限 | リアルタイム | 難易度 |
|---|---|---|---|---|---|
| 1 | `__next` ページング | `/items` | 回避可 | ✅ | ★☆☆ |
| 2 | ID範囲フィルター | `/items?$filter=Id gt N` | 回避可 | ✅ | ★☆☆ |
| 3 | Search REST API | `/_api/search/query` | **影響なし** | ❌ ラグあり | ★★☆ |
| 4 | renderListDataAsStream | `/renderlistdataasstream` | 回避可 | ✅ | ★★☆ |
| 5 | Microsoft Graph API | `/v1.0/sites/.../lists/.../items` | 回避可 | ✅ | ★★☆ |
| 6 | `$skip` ページング | `/items?$skip=N` | 回避可 | ✅ | ★☆☆ |
| 7 | CAML Query (ViewXml) | `/getitems` | 回避可 | ✅ | ★★☆ |
| 8 | インデックス列 + フィルター | `/items?$filter=...` | 予防策 | ✅ | ★☆☆ |

---

## 各手法の詳細

---

### 1. `__next` ページング（最も汎用的・推奨）

`/items` エンドポイントに `$top=5000` を指定し、レスポンスの `d.__next` を次回リクエストURLとして使い回す方法。`__next` には自動的に `$skiptoken` が含まれる。

**ポイント**
- `$top` の最大は **5,000**
- `__next` が `undefined` になったら最終ページ
- `$orderby=Id asc` を付けると安定する

```javascript
async function getAllItems(siteUrl, listTitle) {
  let items = [];
  let url = `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/items`
          + `?$top=5000&$orderby=Id asc`;

  while (url) {
    const res = await fetch(url, {
      headers: { "Accept": "application/json;odata=verbose" }
    });
    const data = await res.json();
    items = items.concat(data.d.results);
    url = data.d.__next || null;
  }

  return items;
}
```

**適用場面:** 全件取得・汎用

---

### 2. ID範囲フィルター（大規模リスト推奨）

`$filter=Id gt N` を使い、取得した最後のIDを次回リクエストの起点にする方法。IDは連続していなくても動作する。

**ポイント**
- `$skip` より高速（内部スキャン不要）
- IDの歯抜けがあっても正しく動作する
- インデックス列（Id）を使うのでしきい値に引っかからない

```javascript
async function getAllItemsById(siteUrl, listTitle, selectFields = "*") {
  const items = [];
  let minId = 0;

  while (true) {
    const url = `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/items`
              + `?$top=5000&$filter=Id gt ${minId}`
              + `&$orderby=Id asc&$select=${selectFields}`;

    const res = await fetch(url, {
      headers: { "Accept": "application/json;odata=verbose" }
    });
    const data = await res.json();
    const results = data.d.results;

    if (results.length === 0) break;
    items.push(...results);
    minId = results[results.length - 1].Id;
  }

  return items;
}
```

**適用場面:** 件数が多い（数万件以上）・高速処理が必要

---

### 3. Search REST API（`_api/search/query`）

SharePoint の**検索インデックス**を使うため、リストビューしきい値の影響を受けない。全テナント横断検索も可能。

**ポイント**
- 5,000件制限の影響を受けない唯一の `/items` 以外の方法
- 検索インデックス更新に **数分〜数時間のラグ** がある
- 取得できるフィールドは **管理プロパティ（Managed Properties）のみ**
- 1回のリクエスト上限は **500件**（`startrow` でページング）
- `TotalRows` で総件数を先に確認するとループ制御が容易

```javascript
async function searchAllItems(siteUrl, listTitle) {
  const results = [];
  const rowLimit = 500;
  let startRow = 0;
  let totalRows = null;

  while (totalRows === null || startRow < totalRows) {
    const url = `${siteUrl}/_api/search/query`
              + `?querytext='contentclass:STS_ListItem AND ListTitle:"${listTitle}"'`
              + `&rowlimit=${rowLimit}`
              + `&startrow=${startRow}`
              + `&selectproperties='Title,ListItemId,Author,Created'`
              + `&trimduplicates=false`;

    const res = await fetch(url, {
      headers: { "Accept": "application/json;odata=verbose" }
    });
    const data = await res.json();
    const queryResult = data.d.query.PrimaryQueryResult.RelevantResults;

    if (totalRows === null) {
      totalRows = queryResult.TotalRows;
    }

    const rows = queryResult.Table.Rows.results;
    if (rows.length === 0) break;
    results.push(...rows);
    startRow += rowLimit;
  }

  return results;
}
```

**適用場面:** 全文検索・複数リスト横断・リアルタイム性が不要な集計

---

### 4. `renderListDataAsStream`（モダン UI 内部 API）

SharePoint モダン UI が内部で使っているエンドポイント。CAML ViewXml と `NextHref` トークンを組み合わせてページングする。

**ポイント**
- CAML クエリをそのまま使える
- `NextHref` に含まれる `Paging` パラメータを次回リクエストに渡す
- POST リクエストのため **RequestDigest** が必要
- フォルダ構造を含むリストでも再帰取得可能（`Scope='RecursiveAll'`）

```javascript
async function getRequestDigest(siteUrl) {
  const res = await fetch(`${siteUrl}/_api/contextinfo`, {
    method: "POST",
    headers: { "Accept": "application/json;odata=verbose" }
  });
  const data = await res.json();
  return data.d.GetContextWebInformation.FormDigestValue;
}

async function getRenderListData(siteUrl, listId) {
  const items = [];
  let pagingInfo = null;
  const digest = await getRequestDigest(siteUrl);

  while (true) {
    const body = {
      parameters: {
        ViewXml: `<View Scope='RecursiveAll'>
          <RowLimit>5000</RowLimit>
          <Query><OrderBy><FieldRef Name='ID'/></OrderBy></Query>
        </View>`,
        ...(pagingInfo && { Paging: pagingInfo })
      }
    };

    const res = await fetch(
      `${siteUrl}/_api/web/lists(guid'${listId}')/renderlistdataasstream`,
      {
        method: "POST",
        headers: {
          "Accept": "application/json;odata=verbose",
          "Content-Type": "application/json;odata=verbose",
          "X-RequestDigest": digest
        },
        body: JSON.stringify(body)
      }
    );

    const data = await res.json();
    items.push(...data.Row);

    if (data.NextHref) {
      const match = data.NextHref.match(/Paging=([^&]+)/);
      pagingInfo = match ? decodeURIComponent(match[1]) : null;
      if (!pagingInfo) break;
    } else {
      break;
    }
  }

  return items;
}
```

**適用場面:** SPFx・モダン UI 連携・フォルダ構造を含むリスト

---

### 5. Microsoft Graph API（`/v1.0/sites/.../lists/.../items`）

Azure AD 認証（OAuth 2.0）を使用するモダンなアプローチ。`@odata.nextLink` で自動ページング。

**ポイント**
- 1回のリクエスト上限は **200件**（`$top=200` 推奨）
- `@odata.nextLink` が存在する間ループ継続
- アクセストークン（Bearer）が必要
- SPFx 以外（Teams タブ・外部アプリなど）に最適
- 列の値は `fields` プロパティに含まれる（`$expand=fields` が必要）

```javascript
async function getGraphItems(siteId, listId, accessToken) {
  const items = [];
  let url = `https://graph.microsoft.com/v1.0/sites/${siteId}/lists/${listId}/items`
          + `?$top=200&$expand=fields`;

  while (url) {
    const res = await fetch(url, {
      headers: { "Authorization": `Bearer ${accessToken}` }
    });
    const data = await res.json();
    items.push(...data.value);
    url = data["@odata.nextLink"] || null;
  }

  return items;
}
```

**サイトID・リストIDの取得方法:**
```
https://graph.microsoft.com/v1.0/sites/{tenant}.sharepoint.com:/sites/{sitename}
https://graph.microsoft.com/v1.0/sites/{siteId}/lists
```

**適用場面:** SPFx 外のモダン開発・Teams アプリ・Power Automate HTTP アクション

---

### 6. `$skip` ページング（シンプルだが注意が必要）

`$skip` でオフセットを指定してページングする最もシンプルな方法。ただし内部的に全件スキャンするため、後半ページほど遅くなる。

**ポイント**
- 実装が最もシンプル
- 件数が増えるほど **後半のページが遅くなる**
- 大規模リスト（1万件超）では非推奨
- `$orderby` を指定しないとページ間でアイテムが重複・欠落することがある

```javascript
async function getAllItemsWithSkip(siteUrl, listTitle) {
  const items = [];
  const top = 1000;
  let skip = 0;

  while (true) {
    const url = `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/items`
              + `?$top=${top}&$skip=${skip}&$orderby=Id asc`;

    const res = await fetch(url, {
      headers: { "Accept": "application/json;odata=verbose" }
    });
    const data = await res.json();
    const results = data.d.results;

    items.push(...results);
    if (results.length < top) break;
    skip += top;
  }

  return items;
}
```

**適用場面:** 件数が比較的少ない（〜1万件程度）・簡単に実装したい場合

---

### 7. CAML Query — `getitems` エンドポイント

`/getitems` エンドポイントに CAML Query を POST して取得する方法。`RowLimit` と `ListItemCollectionPosition` でページングする。

**ポイント**
- 複雑なフィルター・ソートを CAML で記述できる
- `ListItemCollectionPositionNext` をページングトークンとして使用
- POST リクエストのため RequestDigest が必要

```javascript
async function getItemsWithCaml(siteUrl, listTitle) {
  const items = [];
  let positionToken = null;
  const digest = await getRequestDigest(siteUrl);

  while (true) {
    const camlQuery = {
      query: {
        __metadata: { type: "SP.CamlQuery" },
        ViewXml: `<View>
          <RowLimit>5000</RowLimit>
          <Query><OrderBy><FieldRef Name='ID'/></OrderBy></Query>
        </View>`,
        ...(positionToken && {
          ListItemCollectionPosition: {
            __metadata: { type: "SP.ListItemCollectionPosition" },
            PagingInfo: positionToken
          }
        })
      }
    };

    const res = await fetch(
      `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/getitems`,
      {
        method: "POST",
        headers: {
          "Accept": "application/json;odata=verbose",
          "Content-Type": "application/json;odata=verbose",
          "X-RequestDigest": digest
        },
        body: JSON.stringify(camlQuery)
      }
    );

    const data = await res.json();
    items.push(...data.d.results);

    positionToken = data.d.__next
      ? new URL(data.d.__next).searchParams.get("$skiptoken")
      : null;

    if (!positionToken && data.d.results.length === 0) break;
    if (data.d.results.length < 5000) break;
  }

  return items;
}
```

**適用場面:** CAML を活用した複雑なクエリ・既存 CAML 資産の再利用

---

### 8. インデックス列 + フィルター（予防的アプローチ）

根本的な解決策として、**フィルターや並び替えに使う列にインデックスを付与**することで、5,000件制限を回避する。

**インデックスの付け方:**
```
リスト設定 → 「インデックス付き列」→ 「新しいインデックスを作成する」
```

**インデックスあり / なしの比較:**

```javascript
// ✅ インデックスあり列でのフィルター（5,000件超えても動作）
?$filter=Status eq '承認済み'&$top=5000

// ❌ インデックスなし列でのフィルター（5,000件超えるとエラー）
?$filter=CommentText eq 'テスト'
```

**複合インデックスも有効（最大2列）:**
```
例: Status + Created の複合インデックス
→ ?$filter=Status eq '承認済み'&$orderby=Created desc
```

**適用場面:** 特定列での絞り込みが頻繁・設計段階での対策

---

## 手法の選び方フローチャート

```
全件取得したい？
├─ Yes → リアルタイムデータが必要？
│         ├─ Yes → 件数は？
│         │         ├─ ～1万件 → 【手法1: __next ページング】
│         │         └─ 1万件超 → 【手法2: ID範囲フィルター】
│         └─ No  → 【手法3: Search REST API】
│
└─ No（条件付き取得）
    ├─ インデックス列で絞れる → 【手法8: インデックス + フィルター】
    ├─ CAML クエリが必要 → 【手法7: getitems / 手法4: renderListDataAsStream】
    └─ SPFx 外（Teams 等）→ 【手法5: Microsoft Graph API】
```

---

## よくあるエラーと対処

| エラー | 原因 | 対処 |
|---|---|---|
| `The attempted operation is prohibited because it exceeds the list view threshold` | インデックスなし列へのフィルター | インデックス付与 or ID範囲フィルターを使用 |
| `$skip` が途中でデータを飛ばす | `$orderby` 未指定 | `$orderby=Id asc` を追加 |
| Search API でデータが古い | インデックス更新ラグ | リアルタイム用途には `/items` を使用 |
| Graph API で `fields` が空 | `$expand=fields` 未指定 | `?$expand=fields` を追加 |
| `renderListDataAsStream` が403 | RequestDigest 未送信 | `/_api/contextinfo` で Digest を取得 |

---

## パフォーマンス比較（目安）

| 手法 | 10万件取得時間（目安） | メモリ効率 |
|---|---|---|
| `__next` ページング | 60〜120秒 | 中 |
| ID範囲フィルター | 30〜60秒 | 中 |
| Search REST API | 20〜40秒 | 高（非同期） |
| renderListDataAsStream | 40〜80秒 | 中 |
| Microsoft Graph API | 90〜180秒 | 中 |
| `$skip` ページング | 120〜300秒 | 低 |

> ※ 環境・ネットワーク・リスト構造により大きく変動します

---

## まとめ

- **汎用・簡単:** `__next` ページング（手法1）
- **大規模・高速:** ID範囲フィルター（手法2）
- **リアルタイム不要・検索重視:** Search REST API（手法3）
- **CAML 活用・フォルダあり:** renderListDataAsStream（手法4）
- **モダン開発・Teams:** Microsoft Graph API（手法5）
- **設計段階の根本対策:** インデックス列（手法8）

複数の手法を組み合わせることで、あらゆるシナリオに対応できます。
