---
date: 2026-04-24
tags:
  - agent/sharepoint
  - type/📚reference
  - type/📖how-to
  - project/sharepoint
source: https://www.vrdmn.com/2017/07/working-with-page-comments-rest-api-in.html
author: Vardhaman Deshpande
---

# SharePoint Communication サイトのページコメント REST API

SharePoint Communication サイトのモダンページに実装されているコメント機能は、専用の REST API で操作できる。Microsoft（Vesa Juvonen）によりパブリック API として公開が確認されており、サードパーティソリューションでも利用可能。

## 概要

- **対象**: SharePoint Online / Communication サイトのモダンページ
- **エンドポイント**: `/_api/web/lists(...)/GetItemById(...)/Comments`
- **認証**: 標準の SharePoint REST API と同じ（SPFx Webpart で利用可）

## 実装上の重要な仕様

1. **コメントはページのリストアイテムに保存されない**
   別データストアに保存される（スケーラビリティ確保のため）
2. **リスト GUID とアイテム ID で紐付け**
   → ページを移動・コピーするとコメントは失われる
3. **返信は 1 階層のみ**
   深いスレッド化は不可（長い会話を防ぐ設計）
4. **投稿できるのはコメント本文のみ**
   Author・作成日時の上書きは不可（他ユーザーになりすましての投稿は不可能）

## REST API 一覧

### 1. ページのコメント一覧を取得（GUID 指定）

```
GET /_api/web/lists('1aaec881-7f5b-4f82-b1f7-9e02cc116098')/GetItemById(1)/Comments
```

- リスト GUID（Site Pages ライブラリ）とアイテム ID（ページ）を指定
- トップレベルのコメントのみ返る

### 2. リストタイトルで取得（GUID 不要）

```
GET /_api/web/lists/GetByTitle('Site Pages')/GetItemById(1)/Comments
```

### 3. 全コメントと返信を一括取得

```
GET /_api/web/lists('<guid>')/GetItemById(1)/Comments?$expand=replies
```

### 4. 特定コメントの返信を取得

```
GET /_api/web/lists('<guid>')/GetItemById(1)/Comments(2)/replies
```

`Comments(2)` の `2` は対象コメントの ID。

### 5. コメントを投稿

```
POST /_api/web/lists('<guid>')/GetItemById(1)/Comments
```

Body にコメント本文を含める。

### 6. コメント / 返信を削除

```
DELETE /_api/web/lists('<guid>')/GetItemById(1)/Comments(2)
```

コメントと返信は同じエンドポイントで削除可能（それぞれ一意の ID を持つ）。

## SPFx Workbench での動作確認時の注意

コメントを読み書きしたいページが `https://tenant.sharepoint.com/sites/comms/SitePages/ThisIsMyPage.aspx` にある場合、Workbench も **同じサイト配下** で起動する必要がある:

```
https://tenant.sharepoint.com/sites/comms/_layouts/15/workbench.aspx
```

別サイトの Workbench から呼び出すと 404 や 403 が返る。

## ハマりポイント（コメント欄より）

| 症状 | 原因 / 対処 |
|------|------------|
| 404 Object not found | サンプルの GUID をそのまま使用している。Site Pages ライブラリの GUID はサイトごとに異なる。`GetByTitle('Site Pages')` を使うのが安全 |
| 403 Access denied | 呼び出し元のサイトコンテキスト不一致、または権限不足 |
| `$expand=replies` しても `isReply` が常に false | 既知の取得時の挙動。ページング・クエリ条件を見直す |
| コメント投稿者を別ユーザーに変更したい | **不可**。本文のみ POST 可能 |
| コメント投稿時の Webhook | ネイティブには存在しない。モデレーション用途では別実装が必要 |
| クラシックページでの利用 | この API はモダンページ専用 |

## 参考リポジトリ

- [spfx-sitepage-comments (GitHub)](https://github.com/vman/spfx-sitepage-comments/) — SPFx での実装サンプル

## 関連

- [[]]  <!-- sp-rest-api スキル関連ナレッジ -->
- [[]]  <!-- SPFx Webpart 開発関連 -->
- [[]]  <!-- SharePoint 権限・403エラー関連 -->
