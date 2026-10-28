---
updated: 2026-10-28
tags:
  - sharepoint
  - csp
  - scriptloader
  - spfx
---

# SharePoint Online の CSP と「信頼されたスクリプト ソース」

SharePoint Online は **2026-03-01 から CSP（Content Security Policy）を強制**している。
ページが読み込むスクリプトの場所を制限し、許可リストにない場所のスクリプトはブロックされる。
スクリプトを外部から読み込む仕組み（xsp-script-loader、SPFx のデバッグ、CDN 参照）は影響を受ける。

## 何が許可され、何がブロックされるか

| 読み込み方 | CSP の影響 |
|---|---|
| `.sppkg` に含まれる SPFx のバンドル（既定。`ClientSideAssets`） | 影響なし |
| 外部 CDN にホスト（`cdnBasePath`）／`externals` で CDN 参照 | アプリ追加時に信頼されたスクリプト ソースへ**自動追加**される |
| `SPComponentLoader.loadScript('https://...')` で動的読み込み | **手動で許可が必要** |
| インラインスクリプト（`<script>`、`onclick=` 属性、`javascript:` href、`document.write` など） | **ブロックされる**。スクリプトファイルへ移す（nonce は使えない） |
| **`https://localhost:4321`（SPFx ローカルデバッグ）** | **手動で許可が必要** |

- `eval()` は許可（`'unsafe-eval'` が標準ヘッダーに含まれる）
- クラシックページでは CSP は強制されない
- 一覧・ライブラリのページ（AllItems.aspx 等）はキャッシュの影響で反映が遅れる。Ctrl+F5 で強制再読み込み

## 信頼されたスクリプト ソースの管理

### 管理センター

**SharePoint 管理センター > 詳細設定 > スクリプト ソース**（「設定」ではない）。「ソースを追加」から追加・編集する。
「状態」列: アプリカタログから自動追加は「アプリ カタログからインポート済み」、手動は「スクリプト ソースから追加」。

### PowerShell（SPO Management Shell）

```powershell
Get-SPOContentSecurityPolicy
Add-SPOContentSecurityPolicy -Source "https://cdn.host.com/source/"
Remove-SPOContentSecurityPolicy -Source "https://cdn.host.com/source/"
```

CSOM の `Tenant.GetContentSecurityPolicy()` でも操作できる。

### ソース式の書き方

| 目的 | 書き方 |
|---|---|
| 特定のスクリプト 1 本だけ（最も安全） | `https://cdn.jsdelivr.net/npm/jquery@3.6.4/dist/jquery.min.js` |
| ドメイン＋フォルダ配下すべて | `https://cdn.jsdelivr.net/npm/`（**末尾スラッシュ必須**。`/npm/*` は不可） |
| ドメイン全体 | `https://cdn.jsdelivr.net` または `cdn.jsdelivr.net`（`/*` は不可） |
| サブドメインすべて | `*.jsdelivr.net` |

- `*`、`*.domain`（広すぎるもの）、`'unsafe-inline'`、`'wasm-unsafe-eval'`、`'strict-dynamic'` は登録不可
- **最大 300 件**。超えると、アプリカタログへの新しいソリューションのアップロードに影響する。ワイルドカードで集約する
- `cdnBasePath` の末尾スラッシュなしで自動追加されたエントリは、手動でスラッシュを足す

### ローカルデバッグ用に localhost を許可する

```powershell
Add-SPOContentSecurityPolicy -Source "https://localhost:4321"
```

- **テナント全体の設定**で、サイト単位に絞れない。**開発テナントだけに追加し、本番テナントには追加しない**。作業後に削除すると安全
- 詳細な手順・切り分けは [[spfx-debug-local]]（SPFx ナレッジ）

## CSP の動作確認

- 強制の事前確認: ページ URL に `?csp=enforce`（レポートのみは `?csp=report`）
- ブラウザのコンソールに違反が出る。形式: `Loading the script '<URL>' violates the following Content Security Policy directive: "script-src ..."`。**最初に出る URL が違反の原因**
- Microsoft Purview > 監査 > 「Violated Content Security Policy」（`ViolatedContentSecurityPolicy`）で、`DocumentUrl`（違反ページ）と `BlockedUrl`（ブロックされた URL）を確認できる

## テナント設定（PowerShell）

```powershell
Set-SPOTenant -ContentSecurityPolicyEnforcement $true          # 強制を有効化
Set-SPOTenant -DelayContentSecurityPolicyEnforcement $true     # 強制を 90 日遅らせる（2026-06-01 まで）
Set-SPOTenant -ResyncContentSecurityPolicyConfigurationEntries $true  # 自動追加分の再同期（最大 24 時間）
# いずれも、設定後に Get-SPOTenant で値を再取得しないと保存されない（既知の不具合）
```

手動追加したソースは再同期で削除されない。

## xsp-script-loader への影響

xsp-script-loader（Application Customizer）が、SiteAssets 等の JS を読み込む方式によって影響が変わる。

- SharePoint 内（`SiteAssets` など同一オリジン）から読み込む分は、`'self'` で許可される
- 外部ドメインのスクリプトを読み込ませる場合は、そのドメインを信頼されたスクリプト ソースに登録する
- 注入するコードにインラインスクリプトや `onclick=` 属性が含まれていないか確認する（ブロックされる）

出典: Microsoft Learn「Support for Content Security Policy (CSP) in SharePoint Online」
