---
date: 2026-04-24
tags:
  - agent/sharepoint
  - agent/frontend
  - type/📚reference
  - project/sharepoint
---

# SharePoint REST API フィールド一覧

SharePoint REST APIで取得可能な主要フィールドの包括的なリファレンス。エージェント05（sharepoint）および06（frontend）向けの技術情報。

## 概要

SharePoint REST APIを使用してデータを取得する際に利用可能なフィールドを分類・整理。フォルダ、ファイル、リストアイテムそれぞれで取得可能なフィールドが異なる。

## 基本エンドポイント

```
/_api/web/lists/GetByTitle('サイトのページ')/items
```

## フィールド分類

### [1] フォルダ（Folder）で取得できる主なフィールド

| フィールド名 | 説明 |
|-------------|------|
| Name | フォルダ名 |
| ServerRelativeUrl | サイトからの相対パス（例: /sites/demo/Shared Documents/FolderA） |
| ItemCount | 中に含まれるアイテム数 |
| TimeCreated | 作成日時 |
| TimeLastModified | 最終更新日時 |
| UniqueId | フォルダのGUID |
| Exists | フォルダが存在するか |
| WelcomePage | フォルダ内のデフォルトページ（あれば） |

### [2] ファイル（File）で取得できる主なフィールド

| フィールド名 | 説明 |
|-------------|------|
| Name | ファイル名（例: document.docx） |
| ServerRelativeUrl | 相対パス（例: /sites/demo/Shared Documents/document.docx） |
| Length | サイズ（バイト単位） |
| TimeCreated | 作成日時 |
| TimeLastModified | 最終更新日時 |
| Author/Title | 作成者（ユーザー名）※ $expand=Author が必要 |
| ModifiedBy/Title | 更新者（ユーザー名）※ $expand=ModifiedBy が必要 |
| CheckOutType | チェックアウト状態 |
| Exists | 存在するか |
| Title | タイトル（プロパティ上のタイトル） |
| UniqueId | ファイルのGUID |
| ListItemAllFields | 関連するアイテムのメタデータ（例: Created, Editor など） |

### [3] リスト・ライブラリのアイテム（ListItem）で取得できる主なフィールド

| フィールド名 | 説明 |
|-------------|------|
| Id | アイテムID |
| Title | タイトル列の値（存在する場合） |
| FileRef | ファイル名またはパス |
| FileLeafRef | ファイル名またはフォルダ名 |
| FileSystemObjectType | 0=ファイル, 1=フォルダ |
| Created | 作成日時 |
| Author/Title | 作成者（ユーザー名）※ $expand=Author |
| Modified | 更新日時 |
| Editor/Title | 更新者（ユーザー名）※ $expand=Editor |
| ContentTypeId | コンテンツタイプID |
| GUID | アイテムのGUID |
| UniqueId | ファイル/フォルダに紐づくGUID |
| Attachments | 添付ファイルがあるか (true/false) |
| ParentList/Title | 所属リスト名（$expand=ParentList） |

## SharePoint固有の主要フィールド

### 基本情報
- **Id / ID**: アイテムの識別番号（ユニークID）
- **GUID**: アイテムの全世界でユニークな識別番号
- **ContentTypeId**: アイテムの種類を示すコード（ページ、ドキュメントなど）

### ファイル関連
- **FileSystemObjectType**: ファイルかフォルダかを表す（0=ファイル、1=フォルダ）
- **ServerRedirectedEmbedUrl**: ファイルをブラウザで開く場合のURL
- **Title**: ページ本文（キャンバスコンテンツ）
- **CanvasContent1**: モダンページのコンテンツ（ブロックやWebパーツの情報をJSONまたは保存）
- **BannerImageUrl**: ページ上部に表示するバナー画像のURL
- **Description**: ページの説明文
- **OData__OriginalSourceUrl**: 元記事URL（ニュースリンクの場合）
- **OData__SPSiteDraft**: 下書き状態フラグ
- **OData__ModerationStatus**: 承認状態
- **PromotedState**: ニュース記事かどうか（2=公開ニュース）
- **OData__UIVersionString**: ページのバージョン番号

### 日時情報
- **Created**: アイテムが作成された日時
- **Modified**: 最終更新日時
- **FirstPublishedDate**: 最初の公開日時
- **OData__PublishStartDate**: ページの公開開始予定日時

### 人物情報
- **AuthorId**: 作成者のユーザーID
- **EditorId**: 最終更新者のユーザーID
- **OData__AuthorBylineId**: ページの著者情報（複数可能）のユーザーID
- **_AuthorBylineStringId**: ページの著者情報（複数可能）のユーザー名
- **CheckoutUserId**: ファイルをチェックアウト中のユーザーID

### ステータス管理
- **PromotedState**: 昇格状態（0=通常、1=ドラフトニュース、2=公開ニュース）
- **OData__UIVersionString**: ページのバージョン番号

### 翻訳・多言語対応
- **OData__SPSitePageFlags**: ページの特殊フラグ設定
- **OData__SPTranslationLanguage**: ページの言語設定
- **OData__SPTranslatedLanguages**: 翻訳済みの言語リスト
- **OData__SPIsTranslation**: 翻訳版ページかどうか
- **OData__OriginalSourceItemId**: 元のページのID（コピー元）
- **OData__ColorTag**: ページに付けたラベルの色
- **OData__CopySource**: コピー元のページ情報

### メタデータ編集関連
- **_SPHideListEditorMetadataStringId**: メタデータ編集の表示/非表示設定

## 実用的な取得例

### 基本的なニュース記事一覧
```
/_api/web/lists/GetByTitle('サイトのページ')/items?
$select=ID,Title,Description,FileRef,BannerImageUrl,FirstPublishedDate,Created,Modified,GUID,xspT
abCategory,PromotedState&$filter=PromotedState ne 1
```

### フィルター使用例
```
// ニュース記事のみを抽出する例
//(PromotedState eq 2 でニュース記事の分を取得)
/_api/web/lists/GetByTitle('サイトのページ')/items?$filter=PromotedState eq 2
```

### 全フィールドを取得する場合
```
/_api/web/lists/GetByTitle('サイトのページ')/items?$select=*
```

## 使い方のコツ

`m:null="true"` と書いてあるフィールドは空（データなし）という意味です。データを取得する時に `if (value === null)` でチェックして、空の場合の処理を書くといいです！

## REST APIで返される各タグ（フィールド）

SharePoint REST APIの応答で返されるXML形式のフィールドタグ：

### 基本構造
```xml
<content type="application/xml">
  <m:properties>
    <d:FileSystemObjectType m:type="Edm.Int32">
    <d:Id m:type="Edm.Int32">
    <d:ServerRedirectedEmbedUrl m:null="true"/>
    <d:ServerRedirectedEmbedUrl>
    <d:ContentTypeId>
    <d:OData__ColorTag m:null="true"/>
    <d:ComplianceAssetId m:null="true"/>
    <d:WikiField m:null="true"/>
    <d:Title>
    <d:CanvasContent1>
    <d:BannerImageUrl m:type="SP.FieldUrlValue">
    <d:Description>
    <d:PromotedState m:type="Edm.Double">
    <d:FirstPublishedDate m:type="Edm.DateTime">
    <d:LayoutWebpartsContent>
    <d:OData__AuthorBylineId m:type="Collection(Edm.Int32)">
    <!-- その他のフィールド -->
  </m:properties>
</content>
```

## まとめ

| 区分 | RESTエンドポイント | 主なフィールド |
|------|------------------|----------------|
| フォルダ | /folders | Name, ServerRelativeUrl, ItemCount, TimeCreated |
| ファイル | /files | Name, ServerRelativeUrl, Length, TimeCreated, TimeLastModified, Author/Title, ModifiedBy/Title |
| リストアイテム | /items | Id, Title, FileRef, FileLeafRef, Created, Modified, Author/Title, Editor/Title, ContentTypeId |

## 関連

- [[SharePoint REST API 基本操作]]
- [[SharePoint アクセス権限設定]]
- [[Power Automate フロー設計]]
- [[SPFx Web パーツ開発]]