# SharePoint Search API・検索スキーマ・インデックス 調査まとめ

> 対象サイト：`https://u2ayd33s.sharepoint.com/sites/NewsPages`  
> 調査日：2026-07-20

---

## 1. 全体の流れ

```
① ページを作成
    ↓
② クロール（SharePointが自動でページを読みに来る）
    ↓
③ インデックス登録（検索できる形に変換）
    ↓
④ 検索スキーマでマッピング（列名 → 管理プロパティ名に変換）
    ↓
⑤ Search APIで取得可能になる
```

---

## 2. 調査過程で判明した情報

### 2-1. サイト基本情報

| 項目 | 値 |
|---|---|
| サイトURL | `https://u2ayd33s.sharepoint.com/sites/NewsPages` |
| SiteId | `18c412ea-51d7-49af-9464-8a72621da5cc` |
| WebId | `9d6cc31f-09be-4f99-9184-c63793bccf2f` |
| ListId（サイトのページ） | `a3149237-317f-4af9-bce2-56b3da21200d` |

### 2-2. サイトのページ ライブラリ情報

| 項目 | 値 |
|---|---|
| リスト表示名 | `サイトのページ` |
| contentclass | `STS_ListItem_WebPageLibrary` |
| ライブラリ自体のcontentclass | `STS_List_WebPageLibrary`（除外すること） |
| SitePages パス | `/sites/NewsPages/SitePages/` |

### 2-3. カテゴリ列情報

| 項目 | 値 |
|---|---|
| 表示名 | `TabCategory` |
| 内部列名 | `TabCategory` |
| 列の種類 | `Lookup`（参照列） |
| 参照先リスト | `NewsMasteList` |
| もう一つの列 | `xspTabCategory`（Choice列、未使用） |
| クロールプロパティ名 | `ows_TabCategory` |
| プロパティセットID | `00130329-0000-0130-c000-000000131346` |
| カテゴリ（SharePoint） | `SharePoint` |

---

## 3. items API で確認したこと

### 3-1. ページ一覧取得（基本）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/web/lists/getbytitle('サイトのページ')/items
  ?$select=Title,FileRef
  &$top=3
```

### 3-2. カテゴリ付きページ一覧取得（Lookup列は $expand 必須）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/web/lists/getbytitle('サイトのページ')/items
  ?$select=Title,TabCategory/Title
  &$expand=TabCategory
  &$top=5
```

> ⚠️ Lookup列を `$select` に含める場合は必ず `$expand` も必要。  
> ないと `SPException: フィールド 'TabCategory' へのクエリが無効です` エラーになる。

### 3-3. 列一覧取得（内部列名確認用）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/web/lists/getbytitle('サイトのページ')/fields
  ?$select=Title,InternalName,TypeAsString
  &$filter=Hidden eq false and ReadOnlyField eq false
```

---

## 4. Search API で確認したこと

### 4-1. 基本クエリ（ページ一覧）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/search/query
  ?querytext='path:https://u2ayd33s.sharepoint.com/sites/NewsPages/SitePages contentclass:STS_ListItem_WebPageLibrary'
  &rowlimit=50
  &trimduplicates=false
```

### 4-2. 特定ページ狙い撃ち（UniqueID使用）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/search/query
  ?querytext='UniqueID:"97e34a16-a6cf-4c9a-ae0e-643231583de0"'
  &selectproperties='Title,Path,TabCategory'
  &trimduplicates=false
```

> ✅ 日本語ファイル名のURLはエンコード問題で0件になる。UniqueIDで代替する。

### 4-3. カテゴリ付き取得（管理プロパティ登録後）

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/search/query
  ?querytext='path:https://u2ayd33s.sharepoint.com/sites/NewsPages/SitePages contentclass:STS_ListItem_WebPageLibrary'
  &selectproperties='Title,Path,TabCategory,Description,Write,Author,PictureThumbnailURL,ViewsLifeTime,ViewsRecent,SPWebUrl'
  &rowlimit=50
  &trimduplicates=false
  &sortlist='Write:descending'
```

### 4-4. デフォルトで取得できる主なプロパティ

| プロパティ名 | 内容 | 用途 |
|---|---|---|
| `Title` | ページタイトル | カード見出し |
| `Path` | ページURL | リンク先 |
| `Description` | 説明文 | カード本文 |
| `Write` | 更新日時 | 日付表示 |
| `Author` | 作成者 | 著者表示 |
| `PictureThumbnailURL` | サムネイル画像URL | カード画像 |
| `ViewsLifeTime` | 累計閲覧数 | ランキング |
| `ViewsRecent` | 最近の閲覧数 | 人気順 |
| `SPWebUrl` | サイトURL | サイト判別 |
| `ContentTypeId` | コンテンツタイプID | ニュース判別 |
| `contentclass` | コンテンツクラス | 種別判別 |

---

## 5. つまずいたポイントと解決策

| 問題 | 原因 | 解決策 |
|---|---|---|
| Search APIで0件 | `contentclass:STS_Site_Page` が間違い | `STS_ListItem_WebPageLibrary` を使う |
| 日本語URLで0件 | URLエンコード問題 | `UniqueID:"xxx"` で狙い撃ち |
| カテゴリがnull | 管理プロパティ未登録 | 検索スキーマでマッピング設定 |
| Lookup列がエラー | `$expand` 未指定 | `$expand=TabCategory` を追加 |
| `SitePages` パスで0件 | クロール未完了 | インデックス再作成して待機 |

---

## 6. 検索スキーマ設定

### 6-1. 管理プロパティ登録手順

#### STEP1：検索スキーマ画面を開く

```
テナントレベル（推奨）：
https://u2ayd33s-admin.sharepoint.com/_layouts/15/searchadmin/ta_listmanagedproperties.aspx?level=tenant

サイトコレクション単位：
https://u2ayd33s.sharepoint.com/sites/NewsPages/_layouts/15/listmanagedproperties.aspx?level=site
```

#### STEP2：新しい管理プロパティを作成

「管理プロパティ」タブ → 「新しい管理プロパティ」

| 設定項目 | 設定値 |
|---|---|
| プロパティ名 | `TabCategory` |
| 説明 | 任意（例：サイトのページのカテゴリ参照列） |
| 種類 | **テキスト** |
| 検索可能 | ✅ チェック |
| クエリ可能 | ✅ チェック |
| 取得可能 | ✅ チェック |
| 複数の値を許可 | ✅ チェック |
| 絞り込み可能 | ❌ テキスト型はグレーアウト（設定不可） |

#### STEP3：クロールプロパティをマッピング

「マッピング追加」→ `ows_TabCategory` を検索して選択 → OK → 保存

> ⚠️ Lookup列のクロールプロパティ名は環境によって  
> `ows_TabCategory` または `ows_q_LOOKUP_TabCategory` になることがある。

### 6-2. クロールプロパティ確認URL

```
テナントレベル：
https://u2ayd33s-admin.sharepoint.com/_layouts/15/searchadmin/ta_listcrawledproperties.aspx?level=tenant

サイトコレクション単位：
https://u2ayd33s.sharepoint.com/sites/NewsPages/_layouts/15/listmanagedproperties.aspx?level=site
→「クロールされたプロパティ」タブをクリック
```

---

## 7. インデックス再作成

### 7-1. 実行URL

```
https://u2ayd33s.sharepoint.com/sites/NewsPages/_layouts/15/srchvis.aspx
```

### 7-2. 手順

1. 「サイトインデックスの再作成」ボタンをクリック
2. 理由を選択 → **「検索スキーマに変更があります」**
3. 「上記の声明に同意する」チェックボックスをON
4. 「OK」をクリック

### 7-3. 完了時間の目安

| 状況 | 目安 |
|---|---|
| 通常 | 1〜数時間 |
| サーバー負荷高 | 最大24〜48時間 |

---

## 8. 完了確認クエリ

インデックス再作成後、以下で `TabCategory` に値が返ってきたら成功：

```
GET https://u2ayd33s.sharepoint.com/sites/NewsPages/_api/search/query
  ?querytext='path:https://u2ayd33s.sharepoint.com/sites/NewsPages/SitePages contentclass:STS_ListItem_WebPageLibrary'
  &selectproperties='Title,TabCategory'
  &rowlimit=5
  &trimduplicates=false
```

**期待する結果：**
```xml
<d:Key>TabCategory</d:Key>
<d:Value>サイエンス</d:Value>
```

---

## 9. 次のステップ

- [ ] インデックス再作成完了を確認
- [ ] 確認クエリで `TabCategory` の値が取れることを確認
- [ ] 設定管理リスト（`news-sites-config`）に `CategoryField=TabCategory` を記録
- [ ] 16サイト分の設定管理リストを作成
- [ ] JS実装（タブUI・カード表示・カテゴリフィルタ）に着手

---

---

## 参照

- [◆ユーザーが作成した列を検索可能にする : SE備忘録](https://se-note.blog.jp/archives/9628875.html)

---

*作成日：2026-07-20*
