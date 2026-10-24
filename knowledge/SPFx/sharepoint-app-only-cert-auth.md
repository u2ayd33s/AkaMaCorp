---
updated: 2026-10-24
tags: [sharepoint, pnp-powershell, app-only, certificate, auth]
---

# SharePoint にアプリ専用認証（証明書方式）で接続する

開発用の Microsoft 365 テナントでは、**アプリ専用認証（証明書方式）で、サインインなしに SharePoint へ接続できる**。
エージェントが PnP PowerShell を直接実行して、リストやライブラリの確認・登録・更新・削除を行える。

> このリポジトリは公開のため、ClientId・Thumbprint・テナント名・サイト URL は書かない。
> 実際の値は各プロジェクトの非公開リポジトリの接続スクリプトにある（例: `SPFx_Tab-switch` の `testdata/tools/Connect-DevTenant.ps1`）。

## 使い分け

| 方式 | サインイン | 向いている作業 |
|---|---|---|
| アプリ専用認証（証明書） | 不要（エージェントが無人で実行） | リスト・項目の確認、データ投入、重複削除、件数チェック |
| 委任（`-Interactive`） | 必要（ブラウザ。MFA は人が操作） | 初回のアプリ登録、人の権限で動かしたいとき |
| Microsoft 365 コネクタ | claude.ai 側で認証 | 読み取りが中心 |

## 前提と制約

- **開発テナント限定で使う。本番テナントには使わない。** アプリ権限（サイト全体のフルコントロール相当）が付くため。
- PnP.PowerShell 2.x 以降は **PowerShell 7（pwsh）が必須**。Windows PowerShell 5.1 では読み込めない。
  `.ps1` が 5.1 で起動されたら、`pwsh -File` で起動し直す。
- 秘密鍵（PFX）は **Git の管理外**に置く。この PC では `%USERPROFILE%\.pnp-dev\` とユーザー証明書ストア（CurrentUser）。
  PFX・Base64 化した値を、チャット・リポジトリ・ログに貼らない。

## 初回セットアップ（管理者のブラウザ操作が 1 回必要）

```powershell
# pwsh で実行
$out = "$env:USERPROFILE\.pnp-dev"
New-Item -ItemType Directory -Force $out | Out-Null
$p = @{ ApplicationName = "PnP-AppOnly-Dev"; Tenant = "<テナント>.onmicrosoft.com"; OutPath = $out; Store = "CurrentUser" }
Register-PnPEntraIDApp @p
```

- 管理者でサインインして同意する。完了すると **Client ID** と **証明書の Thumbprint** が表示される。
- PnP.PowerShell 3.1.0 の `Register-PnPEntraIDApp` に、権限を指定するパラメーターはない。既定の権限が付く。
- 委任（人のサインイン）用の `Register-PnPEntraIDAppForInteractiveLogin` に `-Interactive` は**ない**（3.x は既定がブラウザ認証）。

## 接続コマンド

```powershell
Import-Module PnP.PowerShell
Connect-PnPOnline -Url https://<テナント>.sharepoint.com/sites/<サイト> `
    -ClientId '<ClientId>' -Thumbprint '<Thumbprint>' -Tenant '<テナント>.onmicrosoft.com'
```

プロジェクトごとに `Connect-Dev` のような関数にまとめておく（例: `Connect-DevTenant.ps1`）。

## エージェントが実行するときの作法

- スクリプトは **ファイルに書いて `pwsh -File` で実行**する。`pwsh -Command "..."` にワンライナーで渡すと、`$` のエスケープで壊れやすい。
- 一時ファイルはスクラッチパッドに置く。
- **削除・上書きの前に対象を確認する。** 例: 重複を消す前に、消す側と残す側のタイトルが 1 対 1 で一致することを確認する。
  `Remove-PnPListItem` / `Remove-PnPList` は `-Recycle`（ごみ箱）を付ける。
- 同じ登録スクリプトを二重に実行しない。実行前に `ItemCount` を確認する。

## 5,000 件を超えるリストを作る

「CSV / Excel から作成」は **5,000 行が上限**（リスト自体の上限は約 3,000 万件）。
リストと列を先に作り、`Add-PnPListItem -Batch` で 100 件ずつ登録する（5,500 件で約 6 分）。

- インデックスは、**空のリストのうちに**作る（5,000 件を超えてからでは作れない）。
- 日付文字列は `[datetime]::ParseExact` が環境によって失敗した。正規表現で分解して `[datetime]::new(...)` を使う。
- 実例: `SPFx_Tab-switch` の `testdata/tools/Import-NewsList.ps1`。

## 関連

- [[spfx-debug-local]]
- 05_sharepoint エージェント: `agents/05_sharepoint/CLAUDE.md`
- 06_SPFx エージェント: `agents/06_SPFx/CLAUDE.md`
