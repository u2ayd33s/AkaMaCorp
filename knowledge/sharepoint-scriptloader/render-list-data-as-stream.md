---
project: sharepoint-scriptloader
tags: [knowledge, sharepoint, rest-api, caml]
created: 2026-05-16
updated: 2026-05-16
---

# sharepoint-scriptloader — ナレッジ: RenderListDataAsStream API と ViewXml

## 概要

SharePoint REST API の `RenderListDataAsStream` エンドポイントは、CAML クエリ（ViewXml）を POST して  
リストデータを JSON で取得する。`getItems` より柔軟なフィルター・ソート・フィールド指定が可能。

---

## エンドポイント

```http
POST {siteUrl}/_api/web/GetList(@listUrl)/RenderListDataAsStream?@listUrl='{encodeURIComponent(listServerRelativeUrl)}'
```

**ヘッダー**

```js
Accept: 'application/json;odata=nometadata'
Content-Type: 'application/json;odata=nometadata'
credentials: 'same-origin'
```

### エンコードの注意

`@listUrl` の値はサーバー相対パスを **1段階** URI エンコードすれば十分。  
`GallerySliderPage.js` では二重エンコードになっているため要注意。

```js
// 正しい例
const endpoint = `${siteUrl}/_api/web/GetList(@listUrl)/RenderListDataAsStream?@listUrl='${encodeURIComponent(listServerRelativeUrl)}'`

// スクリプト内の実装（二重エンコード）
const encodeUrl = encodeURIComponent(LIST_URL);
const endpoint = `...?@listUrl='${encodeURIComponent(encodeUrl)}'`  // ← 不要な二重エンコード
```

---

## POST Body パラメータ

```json
{
  "parameters": {
    "ViewXml": "<View>...</View>",
    "RenderOptions": 2,
    "OverrideViewXml": "<Query><Where>...</Where></Query>",
    "DatesInUtc": true,
    "Paging": ""
  }
}
```

| パラメータ | 型 | 説明 |
|-----------|-----|------|
| `ViewXml` | string | CAML ビュー全体を置換 |
| `OverrideViewXml` | string | 既存ビューの `<Query>` 部分だけ上書き（ViewXml より簡潔） |
| `RenderOptions` | number | 返すデータ種別（ビットマスク） |
| `DatesInUtc` | bool | 日付を UTC で返すか |
| `Paging` | string | ページングトークン |

---

## RenderOptions ビットマスク

| 値 | 定数名 | 意味 |
|----|--------|------|
| `0` | None | デフォルト |
| `1` | ContextInfo | リストのメタ情報 |
| `2` | ListData | データ行（Row 配列）のみ ← **通常はこれ** |
| `4` | ListSchema | フィールド定義情報 |
| `8` | MenuView | ツールバー HTML |
| `16` | ListContentType | コンテンツタイプ詳細（ContextInfo と組み合わせ） |
| `1024` | ViewMetadata | ビュー XML 等のメタデータ |

複数フラグは加算: `3` = ContextInfo(1) + ListData(2)

---

## ViewXml 構造

```xml
<View>
  <Query>
    <Where>
      <!-- フィルタ条件 -->
    </Where>
    <OrderBy>
      <!-- ソート順 -->
    </OrderBy>
    <GroupBy>
      <!-- グループ化（任意） -->
    </GroupBy>
  </Query>
  <ViewFields>
    <FieldRef Name="Title"/>
    <FieldRef Name="FileRef"/>
  </ViewFields>
  <RowLimit Paged="TRUE">100</RowLimit>
</View>
```

- `<Query>` 内の順序: `Where` → `OrderBy` → `GroupBy`
- CAML は **大文字小文字を区別する**（`<Where>` は OK、`<where>` は NG）

---

## CAML 比較演算子

| 要素 | 意味 | 例 |
|------|------|-----|
| `<Eq>` | = | `<Eq><FieldRef Name="Status"/><Value Type="Text">Active</Value></Eq>` |
| `<Neq>` | ≠ | |
| `<Gt>` | > | |
| `<Geq>` | ≥ | `<Geq><FieldRef Name="EndDate"/><Value Type="DateTime"><Today/></Value></Geq>` |
| `<Lt>` | < | |
| `<Leq>` | ≤ | `<Leq><FieldRef Name="StartDate"/><Value Type="DateTime"><Today/></Value></Leq>` |
| `<Contains>` | 文字列を含む | |
| `<BeginsWith>` | 文字列で始まる | |
| `<IsNull>` | NULL | `<IsNull><FieldRef Name="Field"/></IsNull>` |
| `<IsNotNull>` | NULL でない | |
| `<In>` | 値リストに含まれる | |

**論理演算子**: `<And>` / `<Or>`（ネスト可能）

---

## Value の Type 属性

| Type | フィールド型 |
|------|------------|
| `Text` | テキスト |
| `Number` | 数値 |
| `Integer` | 整数 |
| `DateTime` | 日付時刻 |
| `Boolean` | はい/いいえ |
| `Choice` | 選択肢 |
| `Lookup` | ルックアップ |
| `User` | ユーザー |

**DateTime の特殊値**: `<Today/>`, `<Now/>` が使える。

---

## 期間フィルター（StartDate ≤ Today ≤ EndDate）パターン

GallerySliderPage.js で使用している「公開期間内アイテム取得」の定番パターン。

```xml
<Query>
  <Where>
    <And>
      <Leq>
        <FieldRef Name="StartDate"/>
        <Value Type="DateTime"><Today/></Value>
      </Leq>
      <Geq>
        <FieldRef Name="EndDate"/>
        <Value Type="DateTime"><Today/></Value>
      </Geq>
    </And>
  </Where>
  <OrderBy>
    <FieldRef Name="SortOrder" Ascending="TRUE"/>
  </OrderBy>
</Query>
```

---

## レスポンス Row 配列

```json
{
  "Row": [
    {
      "ID": "1",
      "Title": "スライド1",
      "FileLeafRef": "slide1.png",
      "FileRef": "/sites/xsp-docs/Slider/slide1.png",
      "LinkUrl": "https://example.com",
      "SortOrder": "10",
      "FSObjType": "0"
    }
  ]
}
```

- **全フィールドは文字列で返る**（数値も `"10"` 等）。数値が必要な場合は `Number()` 変換が必要。
- `FSObjType`: `"0"` = ファイル、`"1"` = フォルダ
- ユーザーフィールド: `fieldName.title`（表示名）/ `fieldName.id`（ID）で展開される

---

## ViewXml vs OverrideViewXml の使い分け

| | ViewXml | OverrideViewXml |
|--|---------|-----------------|
| 内容 | `<View>` 全体 | `<Query>` 部分のみ |
| 動作 | ビュー設定を完全置換 | 既存ビューの WHERE 条件だけ上書き |
| 用途 | フィールド・RowLimit も制御したい場合 | 既存ビューに動的フィルタを追加したい場合 |

GallerySliderPage.js では `ViewXml` 全体を渡す方式だが、  
サーバーからビュー XML を取得して DOM 操作で書き換えている（L143〜L171）。  
`OverrideViewXml` を使えばその書き換え処理を省略できる。

---

## 実装例（GallerySliderPage.js）

```js
const body = {
  parameters: {
    RenderOptions: 2,
    ViewXml: resultXml   // サーバー取得ビューXMLのQuery部分を差し替えたもの
  }
};

const response = await fetch(endpoint, {
  method: "POST",
  headers: { Accept: 'application/json;odata=nometadata', 'Content-Type': 'application/json;odata=nometadata' },
  credentials: "same-origin",
  body: JSON.stringify(body)
});

const json = await response.json();
const rows = json.Row ?? [];
```

---

## 関連ナレッジ

- [[sharepoint-scriptloader/INDEX]]

## 更新履歴

| 日付 | 更新者（エージェント） | 内容 |
|------|----------------------|------|
| 2026-05-16 | 05_sharepoint | 初版作成（GallerySliderPage.js の実装調査から） |
