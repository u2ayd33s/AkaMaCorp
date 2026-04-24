---
date: 2026-04-24
tags:
  - agent/sharepoint
  - type/📚reference
  - project/SharePoint
---

# どっちの API SHOW？
## SharePoint 開発における Microsoft Graph API と SharePoint REST API の違い

**2025/3/25 — 第10回 JPM365DEV 勉強会**  
発表者：篠原 敬志 (Takashi Shinohara) — アバナード株式会社 グループ マネージャー、アジャイル コーチ  
Microsoft MVP for AI Platform (2024-2025) / Microsoft MVP M365 (2018-2025)

---

> **注意事項**
> - 本資料は 2025/3/25 時点の情報です。今後のバージョンアップにより内容が変更される可能性があります。
> - 比較内容は独自にまとめたものでマイクロソフトの公式見解ではありません。
> - ドキュメントに記載のない API を扱うことがあります。使用される場合はご自身の判断でお願いします。

---

## ROUND 1 — API の種類

### API の調べ方

> 実装コードは [[ms-graph-api/SKILL|ms-graph-api]] / [[sp-rest-api/SKILL|sp-rest-api]] を参照。

- **Microsoft Graph API**: https://learn.microsoft.com/ja-jp/graph/api/overview
- **SharePoint REST API**:
  - https://learn.microsoft.com/ja-jp/sharepoint/dev/sp-add-ins/get-to-know-the-sharepoint-rest-service
  - https://learn.microsoft.com/ja-jp/previous-versions/office/developer/sharepoint-rest-reference

> **凡例**: 🔴 Microsoft Graph API にしかないもの / 🔵 SharePoint REST API にしかないもの

---

### Microsoft Graph API エンドポイント一覧

#### サイト

| 名前 | エンドポイント |
|------|--------------|
| ルートサイトを取得する | `GET /sites/root` |
| サイトを一覧表示する | `GET /sites` |
| サイトを取得する | `GET /sites/{site-id}` |
| 🔴 地域をまたいでサイトを一覧表示する | `GET /sites/getAllSites` |
| サブサイトを一覧表示する | `GET /sites/{site-id}/sites` |
| パスを使用してサイトを取得する | `GET /sites/{hostname}:/{relative-path}` |
| 🔴 グループのサイトを取得する | `GET /groups/{group-id}/sites/root` |
| 🔴 分析を取得する | `GET /sites/{site-id}/analytics/allTime` / `lastSevenDays` |
| 🔴 間隔ごとにアクティビティを取得する | `GET /sites/{site-id}/getActivitiesByInterval` |
| 🔴 デルタを取得する | `GET /sites/delta` |
| サイトを検索する | `GET /sites?search={query}` |
| サイトの列を一覧表示する | `GET /sites/{site-id}/columns` |
| サイトの列を取得する | `GET /sites/{site-id}/columns/{column-id}` |
| サイトの列を作成する | `POST /sites/{site-id}/columns` |
| サイトの列を更新する | `PATCH /sites/{site-id}/columns/{column-id}` |
| サイトの列を削除する | `DELETE /sites/{site-id}/columns/{column-id}` |
| フォローされたサイトを一覧表示する | `GET /me/followedSites` |
| サイトをフォローする | `POST /users/{user-id}/followedSites/add` |
| サイトのフォローを取り消す | `POST /users/{user-id}/followedSites/remove` |
| アクセス許可を一覧表示する | `GET /sites/{site-id}/permissions/{permission-id}` |
| アクセス許可を取得する | `GET /sites/{site-id}/permissions` |
| アクセス許可を追加する | `POST /sites/{site-id}/permissions` |
| アクセス許可を更新する | `PATCH /sites/{site-id}/permissions/{permission-id}` |
| アクセス許可を削除する | `DELETE /sites/{site-id}/permissions/{permission-id}` |
| 🔴 時間のかかる操作を一覧表示する | `GET /sites/{site-id}/operations` |
| 🔴 時間のかかる操作を取得する | `GET /sites/{site-id}/operations/{operation-id}` |

#### リスト

| 名前 | エンドポイント |
|------|--------------|
| リストを一覧表示する | `GET /sites/{site-id}/lists` |
| リストを取得する | `GET /sites/{site-id}/lists/{list-id}` |
| リストを作成する | `POST /sites/{site-id}/lists` |
| リストを更新する | `PATCH /sites/{site-id}/lists/{list-id}` |
| リストを削除する | `DELETE /sites/{site-id}/lists/{list-id}` |
| リストの列を一覧表示する | `GET /sites/{site-id}/lists/{list-id}/columns` |
| リストの列を取得する | `GET /sites/{site-id}/lists/{list-id}/columns/{column-id}` |
| リストの列を作成する | `POST /sites/{site-id}/lists/{list-id}/columns` |
| リストの列を更新する | `PATCH /sites/{site-id}/lists/{list-id}/columns/{column-id}` |
| リストの列を削除する | `DELETE /sites/{site-id}/lists/{list-id}/columns/{column-id}` |
| 🔴 WebSocket エンドポイントを取得する | `GET /sites/{site-id}/lists/{list-id}/drive/root/subscriptions/socketIo` |
| 🔴 時間のかかる操作を一覧表示する | `GET /sites/{site-id}/lists/{list-id}/operations` |
| 🔴 時間のかかる操作を取得する | `GET /sites/{site-id}/lists/{list-id}/operations/{operation-id}` |

#### リストアイテム

| 名前 | エンドポイント |
|------|--------------|
| リストアイテムを一覧表示する | `GET /sites/{site-id}/lists/{list-id}/items` |
| リストアイテムを取得する | `GET /sites/{site-id}/lists/{list-id}/items/{item-id}` |
| リストアイテムを作成する | `POST /sites/{site-id}/lists/{list-id}/items` |
| リストアイテムを更新する | `PATCH /sites/{site-id}/lists/{list-id}/items/{item-id}` |
| リストアイテムを削除する | `DELETE /sites/{site-id}/lists/{list-id}/items/{item-id}` |
| 🔴 分析を取得する | `GET .../analytics/allTime` / `lastSevenDays` |
| 🔴 指定した期間のアクティビティを取得する | `GET .../getActivitiesByInterval` |
| 🔴 ドキュメントセットのバージョンを一覧表示する | `GET .../documentSetVersions` |
| 🔴 ドキュメントセットのバージョンを取得する | `GET .../documentSetVersions/{version-id}` |
| 🔴 ドキュメントセットのバージョンを作成する | `POST .../documentSetVersions` |
| 🔴 ドキュメントセットのバージョンを削除する | `DELETE .../documentSetVersions/{version-id}` |
| 🔴 ドキュメントセットのバージョンを復元する | `POST .../documentSetVersions/{version-id}/restore` |

#### ドライブとドライブアイテム

| 名前 | エンドポイント |
|------|--------------|
| ドライブを一覧表示する | `GET /sites/{site-id}/drives` |
| ドライブを取得する | `GET /sites/{site-id}/drives/{drive-id}` |
| ドライブのルートを取得する | `GET /sites/{site-id}/drives/{drive-id}/root` |
| ドライブアイテムを一覧表示する | `GET .../items/{item-id}/children` |
| ドライブアイテムを取得する | `GET .../items/{item-id}` |
| ドライブアイテムのバージョンを一覧表示する | `GET .../items/{item-id}/versions` |
| フォルダーを作成する | `POST /sites/{site-id}/drive/items/{item-id}/children` |
| ファイルをアップロードする | `POST .../content` |
| ファイルのアップロードセッションを作成する | `POST .../createUploadSession` |
| ファイルをダウンロードする | `GET .../content` |
| 🔴 別の形式でファイルをダウンロードする | `GET .../content?format={format}` |
| ドライブアイテムを更新する | `PATCH .../items/{item-id}` |
| ドライブアイテムを削除する | `DELETE .../items/{item-id}` |
| ドライブアイテムを完全に削除する | `POST .../permanentDelete` |
| ドライブアイテムを移動する | `PATCH .../items/{item-id}` |
| ドライブアイテムをコピーする | `POST .../copy` |
| ドライブアイテムを検索する | `GET .../search(q='{search-text}')` |
| ドライブアイテムをフォローする | `POST .../follow` |
| ドライブアイテムのフォローを取り消す | `POST .../unfollow` |
| 🔴 サムネイルを取得する | `GET .../thumbnails` |
| 🔴 共有リンクを作成する | `POST .../createLink` |
| アクセス許可を一覧表示する | `GET .../permissions` |
| アクセス許可を追加する | `POST .../invite` |
| ドライブアイテムをチェックインする | `POST .../checkin` |
| ドライブアイテムをチェックアウトする | `POST .../checkout` |
| ドライブアイテムのチェックアウトを破棄する | `POST .../discardCheckout` |
| 🔴 ドライブアイテムをプレビューする | `GET .../preview` |
| 🔴 秘密度ラベルを抽出する | `POST .../extractSensitivityLabels` |
| 🔴 秘密度ラベルを割り当てる | `POST .../assignSensitivityLabel` |
| 🔴 保持ラベルを取得する | `GET .../retentionLabel` |
| 🔴 保持ラベルを設定する | `PATCH .../retentionLabel` |
| 🔴 保持ラベルを削除する | `DELETE .../retentionLabel` |
| 🔴 レコードをロック/ロック解除する | `PATCH .../retentionLabel` |

#### コンテンツタイプ

| 名前 | エンドポイント |
|------|--------------|
| コンテンツタイプを一覧表示する | `GET /sites/{site-id}/contentTypes` |
| コンテンツタイプを取得する | `GET .../contentTypes/{contenttype-id}` |
| コンテンツタイプを作成する | `POST .../contentTypes` |
| コンテンツタイプを更新する | `PATCH .../contentTypes/{contenttype-id}` |
| コンテンツタイプを削除する | `DELETE .../contentTypes/{contenttype-id}` |
| 🔴 コンテンツタイプの発行状態を取得する | `GET .../isPublished` |
| 🔴 コンテンツタイプを発行する | `POST .../publish` |
| 🔴 コンテンツタイプの発行を解除する | `POST .../unpublish` |
| コンテンツタイプの列を一覧表示する | `GET .../columns` |
| コンテンツタイプの列を取得する | `GET .../columns/{column-id}` |
| 🔴 コンテンツタイプの列を作成する | `POST .../columns` |
| 🔴 コンテンツタイプの列を更新する | `PATCH .../columns/{column-id}` |
| 🔴 コンテンツタイプの列を削除する | `DELETE .../columns/{column-id}` |
| サイトからリストにコンテンツタイプのコピーを追加する | `POST .../contentTypes/addCopy` |
| 🔴 コンテンツタイプをハブサイトの一覧に関連付ける | `POST .../associateWithHubSites` |
| 🔴 コンテンツタイプの既定のコンテンツの場所にファイルをコピーする | `POST .../copyToDefaultContentLocation` |
| 🔴 互換性のあるコンテンツタイプの一覧を取得する | `GET .../getCompatibleHubContentTypes` |
| 🔴 公開されたコンテンツタイプのコピーをサイトまたはリストに追加または同期する | `POST .../addCopyFromContentTypeHub` |

#### サイトページ

| 名前 | エンドポイント |
|------|--------------|
| 🔴 ベースサイトページを一覧表示する | `GET /sites/{site-id}/pages` |
| 🔴 ベースサイトページを取得する | `GET /sites/{site-id}/pages/{page-id}` |
| 🔴 サイトページを一覧表示する | `GET .../pages/microsoft.graph.sitePage` |
| 🔴 サイトページを取得する | `GET .../pages/{page-id}/microsoft.graph.sitePage` |
| 🔴 サイトページを作成する | `POST /sites/{site-id}/pages` |
| 🔴 サイトページを更新する | `PATCH .../microsoft.graph.sitePage` |
| 🔴 サイトページを削除する | `DELETE /sites/{site-id}/pages/{page-id}` |
| 🔴 サイトページを発行する | `POST .../microsoft.graph.sitePage/publish` |
| 🔴 水平/垂直セクション (CRUD) | `GET/POST/PATCH/DELETE .../canvasLayout/horizontalSections` など |
| 🔴 Webパーツを一覧表示する | `GET .../webparts` |
| 🔴 Webパーツを取得する | `GET .../webParts/{webpart-id}` |
| 🔴 Webパーツを作成する | `POST .../webparts` |
| 🔴 Webパーツを更新する | `PATCH .../webParts/{webpart-id}` |
| 🔴 Webパーツを削除する | `DELETE .../webParts/{webpart-id}` |

#### 用語ストア

| 名前 | エンドポイント |
|------|--------------|
| 🔴 用語ストアを取得する | `GET /sites/{site-id}/termStore` |
| 🔴 用語ストアを更新する | `PATCH sites/{site-id}/termStore` |
| 🔴 用語グループ (CRUD) | `GET/POST/DELETE .../termStore/groups` など |
| 🔴 用語セット (CRUD) | `GET/POST/PATCH/DELETE .../termStore/sets` など |
| 🔴 用語 (CRUD) | `GET/POST/PATCH/DELETE .../sets/{set-id}/terms` など |
| 🔴 用語のリレーション (GET/POST) | `GET/POST .../terms/{term-id}/relations` |

#### テナント管理

| 名前 | エンドポイント |
|------|--------------|
| SharePoint と OneDrive のテナントレベルの設定を取得する | `GET /admin/sharepoint/settings` |
| SharePoint と OneDrive のテナントレベルの設定を更新する | `PATCH /admin/sharepoint/settings` |

---

### SharePoint REST API エンドポイント一覧

#### サイト

| 名前 | エンドポイント |
|------|--------------|
| ルートサイトを取得する | `POST /_api/SPO.Tenant/getRootSiteUrl` |
| サイトコレクションを一覧表示する | `POST /_api/SPO.Tenant/getSitePropertiesFromSharePoint` |
| サイトコレクションを取得する | `POST /_api/SPO.Tenant/getSitePropertiesByUrl` |
| 🔵 サイトコレクションを作成する | `POST /_api/SPSiteManager/create` |
| 🔵 サイトコレクションを削除する | `POST /_api/SPO.Tenant/removeSite` |
| 🔵 削除されたサイトコレクションを一覧表示する | `POST /_api/SPO.Tenant/getDeletedSitePropertiesFromSharePoint` |
| 🔵 削除されたサイトコレクションを取得する | `POST /_api/SPO.Tenant/getDeletedSitePropertiesByUrl` |
| 🔵 削除された個人用サイトを一覧表示する | `POST /_api/SPO.Tenant/getAllDeletedPersonalSitesPropertiesAllVersions` |
| 🔵 削除された個人用サイトを取得する | `POST /_api/SPO.Tenant/getDeletedPersonalSitePropertiesAllVersions` |
| 🔵 サイトコレクションを完全に削除する | `POST /_api/SPO.Tenant/removeDeletedSite` |
| 🔵 サイトコレクションを復元する | `POST /_api/SPO.Tenant/restoreDeletedSite` |
| サイトを取得する | `GET /_api/web` |
| 🔵 サイトを更新する | `POST /_api/web` |
| サブサイトを一覧表示する | `GET /_api/web/webs` |
| 🔵 サブサイトを作成する | `POST /_api/web/webinfos/add` |
| 🔵 サイトを削除する | `DELETE /_api/web` |
| 🔵 権限の継承を削除する | `POST /_api/web/breakRoleInheritance` |
| 🔵 権限の継承をリセットする | `POST /_api/web/resetRoleInheritance` |
| アクセス許可を一覧表示する | `GET /_api/web/roleAssignments` |
| アクセス許可を取得/追加/削除する | `GET/POST/DELETE /_api/web/roleAssignments/...` |
| 🔵 ユーザーを取得する | `GET /_api/web/siteUsers` |
| 🔵 ユーザーを確認する | `POST /_api/web/ensureUser` |
| 🔵 グループを一覧表示する | `GET /_api/web/siteGroups` |
| 🔵 グループを取得/作成/更新/削除する | `GET/POST/PATCH/DELETE /_api/web/siteGroups/...` |
| 🔵 グループにメンバーを追加する | `POST /_api/web/siteGroups/getById({group-id})/users` |
| 🔵 グループからメンバーを削除する | `DELETE /_api/web/siteGroups/getById({group-id})/users/getById({user-id})` |
| 🔵 グループの所有者を設定する | `POST /_api/web/siteGroups/getById({group-id})/setUserAsOwner({user-id})` |
| サイトを検索する | `GET /_api/search/query` |
| サイトの列 (CRUD) | `GET/POST/PATCH/DELETE /_api/web/fields/...` |

#### リスト

| 名前 | エンドポイント |
|------|--------------|
| リストを一覧表示/取得/作成/更新する | `GET/POST/PATCH /_api/web/lists/...` |
| リストを削除する (ごみ箱) | `POST /_api/web/lists/getById('{list-id}')/recycle` |
| 🔵 リストを完全に削除する | `DELETE /_api/web/lists/getById('{list-id}')` |
| 🔵 権限の継承を削除/リセットする | `POST /_api/web/lists/.../breakRoleInheritance` など |
| 🔵 アクセス許可 (CRUD) | `GET/POST/DELETE /_api/web/lists/.../roleAssignments/...` |
| 🔵 ビューを一覧表示/取得/作成/更新/削除する | `GET/POST/PATCH/DELETE /_api/web/lists/.../views/...` |
| リストの列 (CRUD) | `GET/POST/PATCH/DELETE /_api/web/lists/.../fields/...` |

#### リストアイテム

| 名前 | エンドポイント |
|------|--------------|
| リストアイテムを一覧表示/取得/作成/更新する | `GET/POST/PATCH /_api/web/lists/.../items/...` |
| 🔵 ストリームとしてリストアイテムを一覧表示する | `POST .../renderListDataAsStream` |
| リストアイテムを削除する (ごみ箱) | `POST .../items/getById({item-id})/recycle` |
| リストアイテムを完全に削除する | `DELETE .../items/getById({item-id})` |
| 🔵 権限の継承を削除/リセット/アクセス許可 CRUD | 各アイテム配下の `breakRoleInheritance` / `roleAssignments` など |
| リストアイテムのバージョンを一覧表示する | `GET .../versions` |
| 🔵 バージョンを取得/削除/復元する | `GET/DELETE/POST .../versions/getById({version-id})/...` |
| 🔵 添付ファイルの一覧表示/取得/アップロード/ダウンロード/削除 | `/_api/web/lists/.../attachmentFiles/...` |

#### ファイルとフォルダー

| 名前 | エンドポイント |
|------|--------------|
| フォルダー内のフォルダーを一覧表示/取得/作成/変更/削除する | `GET/POST/PATCH/DELETE /_api/web/getFolderByServerRelativeUrl(...)` |
| フォルダー内のファイルを一覧表示する | `GET /_api/web/getFolderByServerRelativeUrl(...)/files` |
| フォルダーのアクセス許可 (CRUD) | `.../roleAssignments/...` |
| ファイルを取得/更新/削除/アップロード/ダウンロードする | `GET/PUT/POST/DELETE /_api/web/getFileByServerRelativeUrl(...)` |
| ファイルのアクセス許可 (CRUD) | `.../roleAssignments/...` |
| ファイルをチェックイン/チェックアウト/チェックアウトを破棄する | `POST .../checkIn` / `checkOut` / `undoCheckOut` |

#### コンテンツタイプ

| 名前 | エンドポイント |
|------|--------------|
| コンテンツタイプ (CRUD) | `GET/POST/PATCH/DELETE /_api/web/contentTypes/...` |
| コンテンツタイプの列を一覧表示/取得する | `GET .../fieldLinks/...` |
| サイトからリストにコンテンツタイプのコピーを追加する | `POST .../addAvailableContentType` |

#### テナント管理

| 名前 | エンドポイント |
|------|--------------|
| テナントの設定を取得する | `GET /_api/SPO.Tenant` |
| テナントの設定を更新する | `PATCH /_api/SPO.Tenant` |

---

### ROUND 1 まとめ

- **Microsoft Graph API** には SharePoint Online の新しい機能に関する API が多い（サイトページ、用語ストア、分析、保持ラベル、秘密度ラベルなど）
- **SharePoint REST API** には SharePoint の設定を操作する API が多い（サイトコレクション作成/削除/復元、グループ管理、ビュー管理、添付ファイルなど）
- **重要な追記**: SharePoint REST API の新しいエンドポイント（`/_api/v2.0/sites`、`/_api/v2.0/drives`、`/_api/v2.1/sites`、`/_api/v2.1/drives`）を使用することで、SharePoint REST API からも Microsoft Graph API の操作を実行できる
  - 参考: https://learn.microsoft.com/ja-jp/sharepoint/dev/apis/sharepoint-rest-graph

---

## ROUND 2 — 認証とスコープ

### スコープの比較：委任されたアクセス許可

> 権限設計・実装コードは [[sp-permissions/SKILL|sp-permissions]] を参照。

| スコープの種類 | Microsoft Graph API | SharePoint REST API |
|--------------|--------------------|--------------------|
| サイト | `Sites.Read.All` | `AllSites.Read` |
| | `Sites.ReadWrite.All` | `AllSites.Write` |
| | `Sites.Manage.All` | `AllSites.Manage` |
| | `Sites.FullControl.All` | `AllSites.FullControl` |
| | `Sites.Selected` 🔴 | — |
| リスト | `Lists.SelectedOperations.Selected` 🔴 | — |
| リストアイテム | `ListItems.SelectedOperations.Selected` 🔴 | — |
| ファイル | `Files.Read` | `MyFiles.Read` |
| | `Files.ReadWrite` | `MyFiles.Write` |
| | `Files.Read.All` 🔴 | — |
| | `Files.ReadWrite.All` 🔴 | — |
| | `Files.Read.Selected` 🔴 | — |
| | `Files.SelectedOperations.Selected` 🔴 | — |
| 検索 | — | `Sites.Search.All` 🔵 |
| 用語ストア | `TermStore.Read.All` | `TermStore.Read.All` |
| | `TermStore.ReadWrite.All` | `TermStore.ReadWrite.All` |

### スコープの比較：アプリケーションのアクセス許可

| スコープの種類 | Microsoft Graph API | SharePoint REST API |
|--------------|--------------------|--------------------|
| サイト | `Sites.Read.All` | `Sites.Read.All` |
| | `Sites.ReadWrite.All` | `Sites.Write.All` |
| | `Sites.Manage.All` | `Sites.Manage.All` |
| | `Sites.FullControl.All` | `Sites.FullControl.All` |
| | `Sites.Archive.All` 🔴 | — |
| | `Sites.Selected` | `Sites.Selected` |
| リスト | `Lists.SelectedOperations.Selected` 🔴 | — |
| リストアイテム | `ListItems.SelectedOperations.Selected` 🔴 | — |
| ファイル | `Files.Read.All` 🔴 | — |
| | `Files.ReadWrite.All` 🔴 | — |
| | `Files.ReadWrite.AppFolder` 🔴 | — |
| | `Files.SelectedOperations.Selected` 🔴 | — |
| 用語ストア | `TermStore.Read.All` | `TermStore.Read.All` |
| | `TermStore.ReadWrite.All` | `TermStore.ReadWrite.All` |

### 委任されたアクセス許可とアプリケーションのアクセス許可

**委任されたアクセス許可**
- アプリケーションにログインしたユーザーとして動作する
- アプリケーションに付与されたアクセス許可とユーザーに付与されたアクセス許可の最小範囲のリソースにのみアクセス可能

**アプリケーションのアクセス許可**
- アプリケーションそのものとして動作する（いわゆるシステムアカウント）
- 付与されたアクセス許可に対するすべてのリソースにアクセス可能
- 取り扱いには注意が必要

参考: https://learn.microsoft.com/ja-jp/entra/identity-platform/permissions-consent-overview

### リソース固有のアクセス許可 (RSC)

> RSC の設定手順・コード例は [[sp-permissions/SKILL|sp-permissions]] を参照。

特定のサイト・リスト・リストアイテムに対してのみアクセス許可を絞り込める機能。  
`Sites.Selected` を使用し、実際にアクセス可能なリソースは Microsoft Graph API で設定する。

設定可能なエンドポイント例：
- `/sites/{site-id}/permissions`
- `/sites/{site-id}/lists/{list-id}/permissions`
- `/sites/{site-id}/lists/{list-id}/items/{item-id}/permissions`
- `/drives/{drive-id}/items/{item-id}/permissions`

参考: https://learn.microsoft.com/ja-jp/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online

### アプリケーションのアクセス許可での証明書とシークレット

| | Microsoft Graph API | SharePoint REST API |
|--|--------------------|--------------------|
| シークレット | ✅ 使用可能 | ❌ 使用不可（`Unsupported app only token` エラー） |
| 証明書 | ✅ 使用可能 | ✅ 使用可能（必須） |

> SharePoint REST API でアプリケーションのアクセス許可を使用する場合は**証明書が必須**。

参考: https://learn.microsoft.com/ja-jp/sharepoint/dev/solution-guidance/security-apponly-azuread

### ROUND 2 まとめ

- **Microsoft Graph API** は SharePoint REST API に比べてスコープの種類が多い（ファイルスコープ、リソース固有スコープなど）
- **SharePoint REST API** はアプリケーションのアクセス許可で証明書しか使用できない
- **勝者: Microsoft Graph API** 🏆

---

## ROUND 3 — リクエストの調整（スロットリング）

### ユーザーレベルのリクエスト調整

| 調整の種類 | 間隔 | 制限 |
|----------|------|------|
| 1秒あたりのリクエスト回数 | 5分ごと | 3,000 回 |
| イングレス | 1時間ごと | 50 GB |
| エグレス | 1時間ごと | 100 GB |
| 委任されたトークンの要求回数 | 5分ごと | 50 回 |
| 外部共有メール | 1時間ごと | 200 通 |

### テナントレベルのリクエスト調整

| ライセンス数 | 0–1,000 | 1,001–5,000 | 5,001–15,000 | 15,001–50,000 | 50,000– |
|------------|---------|------------|-------------|--------------|---------|
| 5分ごと | 18,750 RU | 37,500 RU | 56,250 RU | 75,000 RU | 93,750 RU |

### アプリケーションレベルのリクエスト調整

| ライセンス数 | 0–1,000 | 1,001–5,000 | 5,001–15,000 | 15,001–50,000 | 50,000– |
|------------|---------|------------|-------------|--------------|---------|
| 1分ごと | 1,250 RU | 2,500 RU | 3,750 RU | 5,000 RU | 6,250 RU |
| 24時間ごと | 1,200,000 RU | 2,400,000 RU | 3,600,000 RU | 4,800,000 RU | 6,000,000 RU |

### リソースユニット (RU) の計算方法

| 要求あたりの RU 数 | 操作 |
|-----------------|------|
| 1 | アイテムの取得などの単一アイテムクエリ、トークンを使用した差分、ファイルのダウンロード |
| 2 | リストの子などの複数項目クエリ、作成・更新・削除・アップロード |
| 5 | すべてのアクセス許可リソース操作（`$expand=permissions` を含む） |

### 調整を処理するためのベストプラクティス

- 同時要求の数を減らす
- 要求の急増を回避する
- **可能な場合は SharePoint REST API よりも Microsoft Graph API を使用する**
- `Retry-After` および `RateLimit` ヘッダーを使用する
- HTTP トラフィックを装飾する

**Microsoft Graph API を推奨する理由**
- SharePoint REST API では RU 以外の内部調整を受ける可能性がある
- 一般的に Microsoft Graph API のほうがリソースの消費量が少ない

参考: https://learn.microsoft.com/en-us/sharepoint/dev/general-development/how-to-avoid-getting-throttled-or-blocked-in-sharepoint-online

### ROUND 3 まとめ

- SharePoint REST API と Microsoft Graph API では同じリクエスト調整が発生する
- SharePoint REST API は追加の内部調整が発生する可能性がある
- Microsoft Graph API のほうがリソースの消費量が少ない
- **勝者: Microsoft Graph API** 🏆

---

## APPENDIX — Microsoft Graph API と SharePoint REST API の併用

### 複数のサービスに対する Entra ID アプリケーションの制限

- **Microsoft Graph API と SharePoint REST API には同じアクセストークンは使えない**
  - アクセストークンの `aud`（オーディエンス）が異なるため
  - 複数サービスのスコープを同一トークンに含めることもできない
  - MSAL では複数のアクセストークンの取得をサポートしない

**解決策: On-Behalf-Of フロー**（実装は [[ms-graph-api/SKILL|ms-graph-api]] を参照）

Web API から他の Web API にアクセスするための OAuth フロー。

```
Web App
  → (1) Web API へのアクセストークンを取得 → Entra ID
  → (2) Web API にリクエスト → Web API
                                    → (3) アクセストークンを交換 → Entra ID
                                    → (4) Microsoft Graph API / SharePoint REST API にリクエスト
  ← (5) Web App にレスポンス
```

参考: https://learn.microsoft.com/ja-jp/entra/identity-platform/v2-oauth2-on-behalf-of-flow

---

## 総まとめ

| ユースケース | 推奨 API |
|------------|---------|
| SharePoint の機能を使い倒したい（グループ、ビュー、添付ファイル、サイトコレクション管理など） | **SharePoint REST API** |
| 細かいセキュリティやアクセス制御をしたい（RSC、ファイルスコープ、秘密度ラベルなど） | **Microsoft Graph API** |
| 大量リクエストが発生する可能性がある | **Microsoft Graph API** |
| アプリオンリー認証（証明書なし・シークレットのみ） | **Microsoft Graph API** |
| サイトページや Web パーツをプログラム操作したい | **Microsoft Graph API** |

---

## 関連ノート

### エージェント
- [[agents/05_sharepoint/CLAUDE|05_sharepoint]] — SharePoint 担当エージェント（このナレッジの主要読者）

### スキル
- [[ms-graph-api/SKILL|ms-graph-api]] — Microsoft Graph API 認証・エンドポイント実装コード
- [[sp-rest-api/SKILL|sp-rest-api]] — SharePoint REST API CRUD・フィルター・バッチ処理実装
- [[sp-permissions/SKILL|sp-permissions]] — サイト権限・RSC・アクセス管理の設計と実装