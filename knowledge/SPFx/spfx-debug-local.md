---
updated: 2026-05-09
tags:
  - spfx
  - debug
  - sharepoint
---

# SPFx ローカルデバッグ手順（実ページ使用）

## 概要

SPFx 1.13 以降、ローカル Workbench (`https://localhost:4321/temp/workbench.html`) は廃止された。
実際の SharePoint モダンページにデバッグクエリを付けて動作確認する。

## 手順

### 1. ビルドサーバー起動

```bash
cd SPFx/custom-html-webpart
gulp serve --nobrowser
```

- `--nobrowser`: ブラウザを自動で開かない（Workbench が廃止されているため必須）
- 起動完了のサイン:

```
[spfx-serve] To load your scripts, use this query string:
?debug=true&noredir=true&debugManifestsFile=https://localhost:4321/temp/manifests.js
Starting server...
Finished subtask 'spfx-serve'
```

ポート 4321 で HTTPS サーバーが起動する。

### 2. 実ページを開く

デバッグしたい SharePoint ページの URL に以下のクエリを追加:

```
?loadSPFX=true&debugManifestsFile=https://localhost:4321/temp/manifests.js
```

例:

```
https://u2ayd33s.sharepoint.com/sites/MySite/SitePages/MyPage.aspx?loadSPFX=true&debugManifestsFile=https://localhost:4321/temp/manifests.js
```

### 3. 「デバッグスクリプトを読み込む？」ダイアログを許可

ページが開くと SharePoint からダイアログが表示される:

> **デバッグスクリプトを読み込みますか？**
> 「許可」または「Allow」を選択

→ ローカルビルドのウェブパートが実ページに読み込まれる。

### 4. ウェブパートを追加して動作確認

ページ編集モードで「+」からウェブパートを追加。
デバッグビルドのウェブパートが一覧に表示される。

## トラブルシューティング

### `EADDRINUSE: address already in use ::1:4321`

ポート 4321 が別プロセスに占有されている。

**確認（PowerShell）:**

```powershell
Get-Process -Id (Get-NetTCPConnection -LocalPort 4321).OwningProcess
```

**停止（PowerShell）:**

```powershell
Stop-Process -Id <PID> -Force
```

Git Bash から `taskkill /PID <PID> /F` は動作しない場合があるため PowerShell を使う。

### 証明書エラー

`https://localhost:4321` にアクセスしたとき証明書警告が出る場合は一度直接アクセスして「詳細設定 → 続行」を選択しておく。

### ウェブパートが一覧に表示されない

- gulp serve が正常に起動しているか確認（ターミナルにエラーがないか）
- ページをリロード（Ctrl+R）してダイアログを再度許可

## gulp vs npm の違い

SPFx のビルドツールチェーンは gulp ベース（v1.20 時点）。

| コマンド | 用途 |
|---------|------|
| `gulp serve --nobrowser` | ローカル開発サーバー起動（実ページデバッグ用） |
| `gulp build` | ビルドのみ（サーバーなし） |
| `gulp bundle --ship` | 本番ビルド |
| `gulp package-solution --ship` | .sppkg 生成 |

## serve.json 設定

`config/serve.json`:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/spfx-build/spfx-serve.schema.json",
  "port": 4321,
  "https": true,
  "initialPage": "https://u2ayd33s.sharepoint.com/_layouts/workbench.aspx"
}
```

`initialPage` は `--nobrowser` で無視されるが、`gulp serve`（ブラウザあり）のときに開くページを指定する。
