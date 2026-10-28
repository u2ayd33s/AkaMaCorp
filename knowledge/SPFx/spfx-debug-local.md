---
updated: 2026-10-28
tags:
  - spfx
  - debug
  - sharepoint
  - csp
---

# SPFx ローカルデバッグ手順（実ページ使用）

## 概要

SPFx 1.13 以降、ローカル Workbench は廃止された。実際の SharePoint モダンページにデバッグクエリを付けて動作確認する。
`.sppkg` の再パッケージ・アップロードは不要。ソース保存で自動再ビルドされ、ページ再読み込みで反映される。

> **2026-03-01 以降は SharePoint Online の CSP 強制により、事前準備（下記「0」）が必須。** 準備なしだと `Script error for ".../manifests.js"` になる。

## 事前準備（初回のみ・必須）

### 0-1. 信頼されたスクリプト ソースに localhost を追加

SharePoint Online の CSP が `https://localhost:4321` のスクリプトをブロックするため、許可を追加する。

- 管理センター: **SharePoint 管理センター > 詳細設定 > スクリプト ソース > ソースを追加** → `https://localhost:4321`
  - 「設定」配下ではない。「詳細設定」にある
  - 末尾スラッシュや `*` は付けない（`*` など広すぎる指定は不可）
- PowerShell（SPO Management Shell）でも可:

```powershell
Connect-SPOService -Url https://<tenant>-admin.sharepoint.com
Add-SPOContentSecurityPolicy -Source "https://localhost:4321"
Get-SPOContentSecurityPolicy
# 削除: Remove-SPOContentSecurityPolicy -Source "https://localhost:4321"
```

- **テナント全体に効く設定**（サイト単位に絞れない）。**開発テナントだけに追加する。本番テナントには追加しない**。開発が終わったら削除するとより安全
- 反映に時間がかかることがある。効かないときはページを Ctrl+F5 で強制再読み込み

### 0-2. ブラウザのローカルネットワークアクセス許可

Chromium 142 以降の Edge / Chrome は、公開サイト（SharePoint）から localhost への通信を許可制にしている。
「このサイトがローカル ネットワーク上のデバイスにアクセスすることを許可しますか?」と出たら **許可** する。

- 後から変更: アドレスバー左の鍵アイコン > サイトの設定 > ローカル ネットワーク アクセス
- 設定ページ: `edge://settings/content/localNetworkAccess` / `chrome://settings/content/localNetworkAccess`

### 0-3. 開発証明書の信頼

```powershell
npx gulp trust-dev-cert
```

`https://localhost:4321/temp/manifests.js` を新しいタブで直接開いて、JavaScript の中身が表示されれば証明書は問題ない。

## 手順

### 1. ビルドサーバー起動

Node は **SPFx が対応するバージョン（1.20 なら Node 20）** で実行する。既定が Node 24 などだと gulp が失敗する。

```powershell
$env:PATH = "$env:LOCALAPPDATA\nvm\v20.15.1;$env:PATH"   # nvm の場合
cd <プロジェクト>
npx gulp serve --nobrowser
```

- `--nobrowser`: ブラウザを自動で開かない（Workbench が廃止されているため必須）
- 起動完了のログに、使うべきクエリが出る:

```
[spfx-serve] To load your scripts, use this query string:
?debug=true&noredir=true&debugManifestsFile=https://localhost:4321/temp/manifests.js
```

### 2. 実ページを開く

デバッグしたい SharePoint ページの URL に、**ログに出たクエリをそのまま**付ける。

| SPFx バージョン | debugManifestsFile |
|---|---|
| 1.20 以前（gulp） | `https://localhost:4321/temp/manifests.js` |
| **1.21 以降（heft）** | `https://localhost:4321/temp/build/manifests.js`（パスが変わった） |

```
https://<tenant>.sharepoint.com/sites/MySite/SitePages/MyPage.aspx?debug=true&noredir=true&debugManifestsFile=https%3A%2F%2Flocalhost%3A4321%2Ftemp%2Fmanifests.js
```

Web パーツ（既存ページに配置済み）は `?loadSPFX=true` なしでも `debugManifestsFile` だけで読み込まれる。拡張機能は `loadSPFX=true` と `customActions` も必要。

### 3. 「デバッグ スクリプトを読み込みますか?」を許可

ダイアログで **許可（Load debug scripts）** を選ぶ。選ばないと、アプリカタログ版（デプロイ済みの旧版）のままで、修正が反映されない。

### 4. 反映確認

- 開発者ツール > ネットワークで `localhost:4321` へのリクエストが成功していること
- 変更が見えない場合: クエリが URL に残っているか、Ctrl+F5 で強制再読み込み、InPrivate で再確認

## トラブルシューティング

### `Script error for "https://localhost:4321/temp/manifests.js"`（requirejs のエラー）

`manifests.js` を直接開くと見えるのに、SharePoint ページ経由だけエラーになる場合の切り分け。**まず開発者ツールのコンソールの最初の赤いエラーを見る。**

| コンソールのメッセージ | 原因 | 対処 |
|---|---|---|
| `Loading the script 'https://localhost:4321/...' violates the following Content Security Policy directive` | **CSP（信頼されたスクリプト ソース未登録）** | 事前準備 0-1 |
| ローカル ネットワーク アクセスの許可ダイアログが出た／ブロックされた | Chromium 142+ のローカルネットワークアクセス | 事前準備 0-2 |
| `ERR_CERT_AUTHORITY_INVALID` など証明書エラー | 開発証明書が未信頼 | 事前準備 0-3 |
| `ERR_CONNECTION_REFUSED` | gulp serve が起動していない／ポート違い | 起動確認。下記 IPv4/IPv6 も参照 |

> 2026-10 の実例: 原因は CSP だった。ネットワーク・証明書・IPv6 を先に疑って遠回りしたので、**最初にコンソールのエラー文を読む**こと。

### localhost が IPv6 (`::1`) だけで待ち受ける

Node 17 以降は `localhost` を `::1` 優先で解決するため、gulp serve が IPv6 のみで待ち受けることがある。
確認: `Get-NetTCPConnection -LocalPort 4321 -State Listen`（`LocalAddress` が `::1` のみ）。
ブラウザが IPv4 で解決すると届かないため、必要なら IPv4 優先で起動する:

```powershell
$env:NODE_OPTIONS = "--dns-result-order=ipv4first"
npx gulp serve --nobrowser
```

（今回の CSP エラーの原因ではなかったが、接続系の切り分けでは確認する価値がある）

### `EADDRINUSE: address already in use ::1:4321`

ポート 4321 が別プロセスに占有されている。

```powershell
Get-Process -Id (Get-NetTCPConnection -LocalPort 4321).OwningProcess
Stop-Process -Id <PID> -Force
```

Git Bash から `taskkill /PID <PID> /F` は動作しない場合があるため PowerShell を使う。

### ウェブパートが一覧に表示されない

- gulp serve が正常に起動しているか確認（ターミナルにエラーがないか）
- ページをリロードしてダイアログを再度許可

## gulp vs npm の違い

SPFx のビルドツールチェーンは v1.20 まで gulp ベース、v1.21 以降は heft ベース。

| コマンド | 用途 |
|---------|------|
| `gulp serve --nobrowser`（1.21+ は `heft start --nobrowser`） | ローカル開発サーバー起動（実ページデバッグ用） |
| `gulp build` | ビルドのみ（サーバーなし） |
| `gulp bundle --ship` | 本番ビルド（lint 警告で exit 1 になるが成果物は出る） |
| `gulp package-solution --ship` | .sppkg 生成（バージョンを上げないとアプリカタログが更新されない） |

## serve.json 設定

`config/serve.json`:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/spfx-build/spfx-serve.schema.json",
  "port": 4321,
  "https": true,
  "initialPage": "https://<tenant>.sharepoint.com/_layouts/workbench.aspx"
}
```

`initialPage` は `--nobrowser` で無視されるが、`gulp serve`（ブラウザあり）のときに開くページを指定する。

## 関連

- [[sharepoint-csp-trusted-script-sources]]（sharepoint-scriptloader ナレッジ）— CSP と信頼されたスクリプト ソースの仕様
- [[custom-html-webpart]] — インラインスクリプトと CSP の記述ルール
- 出典: Microsoft Learn「Debug SharePoint Framework solutions on modern SharePoint pages」「Support for Content Security Policy (CSP) in SharePoint Online」
