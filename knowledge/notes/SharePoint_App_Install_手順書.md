# SharePoint サブサイトへのアプリインストール手順書

> ブラウザのコンソール（F12）から REST API を使ってアプリをインストールする方法

---

## 前提条件

| 項目 | 内容 |
|------|------|
| AppCatalog | サイトコレクションのAppCatalogに `.sppkg` がアップロード済み |
| 展開設定 | AppCatalogで「展開済み：はい」「すべてのサイトに追加：はい」になっていること |
| 権限 | インストール対象サイトのサイト管理者権限 |

---

## AppCatalog の確認

以下のURLにアクセスして、インストールしたいアプリの **製品ID（GUID）** を控えておく。

```
https://{テナント名}.sharepoint.com/sites/{サイト名}/AppCatalog/Forms/AllItems.aspx
```

**確認する列：**

| 列名 | 確認内容 |
|------|----------|
| 展開済み | `はい` になっているか |
| すべてのサイトに追加 | `はい` になっているか |
| アプリケーションパッケージのエラーメッセージ | `エラーはありません。` になっているか |
| 製品ID | インストールに使用するGUID（例：`3BEF2208-7908-4438-B70E-CEBEBA7219D1`） |

---

## インストール手順

### Step 1：対象サブサイトをブラウザで開く

```
https://{テナント名}.sharepoint.com/sites/{サイト名}/{サブサイト名}
```

例：
```
https://u2ayd33s.sharepoint.com/sites/Share-Point/mare-group
```

---

### Step 2：ブラウザの開発者ツールを開く

```
キーボード： F12
または
右クリック → 「検証」
```

**Console タブ** を選択する。

---

### Step 3：以下のコードを貼り付けて実行

```javascript
// インストール先サブサイトのURL
const siteUrl = "https://{テナント名}.sharepoint.com/sites/{サイト名}/{サブサイト名}";

// AppCatalogの製品ID（GUIDを控えたもの）
const appId = "{製品ID}";

// RequestDigest を取得してからアプリをインストール
fetch(`${siteUrl}/_api/contextinfo`, {
  method: "POST",
  headers: { "Accept": "application/json;odata=verbose" }
})
.then(r => r.json())
.then(ctx => {
  const digest = ctx.d.GetContextWebInformation.FormDigestValue;
  console.log("Digest取得成功:", digest.substring(0, 30) + "...");

  return fetch(`${siteUrl}/_api/web/sitecollectionappcatalog/AvailableApps/GetById('${appId}')/Install`, {
    method: "POST",
    headers: {
      "Accept": "application/json;odata=verbose",
      "X-RequestDigest": digest
    }
  });
})
.then(r => {
  console.log("ステータス:", r.status);
  return r.text();
})
.then(console.log)
.catch(console.error);
```

---

### Step 4：実行結果を確認

**成功時：**
```
Digest取得成功: 0xA076E1DF28D536014DE750FCA841...
ステータス: 200
{"d":{"Install":null}}
```

**失敗時の主なエラー：**

| エラー | 原因 | 対処 |
|--------|------|------|
| `セキュリティ検証は無効` | RequestDigestが空 | 修正版コード（contextinfo取得）を使う |
| `ステータス: 404` | appIdが間違っている | AppCatalogで製品IDを再確認 |
| `ステータス: 403` | 権限不足 | サイト管理者でログインし直す |

---

### Step 5：インストール確認

以下のURLにアクセスして、サイトコンテンツにアプリが表示されていれば完了。

```
https://{テナント名}.sharepoint.com/sites/{サイト名}/{サブサイト名}/_layouts/15/viewlsts.aspx
```

---

## 実行例（u2ayd33s環境）

| 項目 | 値 |
|------|----|
| サブサイトURL | `https://u2ayd33s.sharepoint.com/sites/Share-Point/mare-group` |
| アプリ名 | xsp-script-loader |
| 製品ID | `3BEF2208-7908-4438-B70E-CEBEBA7219D1` |
| 結果 | ステータス 200 / インストール成功 |

---

## 補足：なぜ PowerShell が使えなかったか

| 方法 | 結果 | 理由 |
|------|------|------|
| `Install-SPOApp` | ❌ コマンドなし | SPO管理シェルにアプリインストールコマンドが存在しない |
| `Add-PnPApp` | ❌ 接続失敗 | グループポリシーによりPnP接続がブロックされている |
| REST API（コンソール） | ✅ 成功 | ブラウザのログイン済みセッションを利用するため制限を受けない |
