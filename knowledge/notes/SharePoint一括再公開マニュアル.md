# SharePoint ページ・ニュース 一括再公開マニュアル

## 概要

PnP PowerShellを使って、SharePointサイト内のすべてのページ（通常ページ＋ニュース）を一括で再公開する手順をまとめたものです。

**実績**: 267ページを約6分で再公開完了（2026年4月実施）

---

## 目次

1. [事前準備](#事前準備)
2. [初回セットアップ：Entra IDアプリ登録](#初回セットアップentra-idアプリ登録)
3. [SharePointサイトへの接続](#sharepointサイトへの接続)
4. [対象ページの確認](#対象ページの確認)
5. [テスト実行（推奨）](#テスト実行推奨)
6. [本番実行：一括再公開](#本番実行一括再公開)
7. [エラー対応](#エラー対応)
8. [トラブルシューティング](#トラブルシューティング)
9. [用語解説](#用語解説)

---

## 事前準備

### 必要な環境

- Windows PowerShell 5.1 または PowerShell 7.x
- PnP PowerShell モジュール
- SharePointサイトへのアクセス権限（最低でも編集者権限）
- Entra ID（旧Azure AD）でアプリ登録できる権限（初回のみ）

### PnP PowerShellのインストール

未インストールの場合は、PowerShellを管理者として起動して以下を実行：

```powershell
Install-Module -Name PnP.PowerShell -Scope CurrentUser
```

---

## 初回セットアップ：Entra IDアプリ登録

PnP PowerShell 2.x以降では、自前のClient IDが必須になりました。**この作業は初回のみ**でOKです。

### コマンド

```powershell
Register-PnPEntraIDAppForInteractiveLogin -ApplicationName "PnP PowerShell" -Tenant "テナント名.onmicrosoft.com"
```

### 各パラメータの意味

| パラメータ | 説明 |
|-----------|------|
| `-ApplicationName` | Entra IDに登録するアプリの表示名（任意の名前） |
| `-Tenant` | テナント名。`xxx.onmicrosoft.com` の形式 |

### 実行の流れ

1. ブラウザが開いてサインイン画面が表示される
2. **グローバル管理者または特権ロール管理者**でサインイン
3. アプリの権限について同意（Admin Consent）を求められる
4. 完了するとClient ID（GUID）が表示される

### ⚠️ 重要

表示された**Client IDは必ずメモ**しておくこと。次回以降の接続で必要になります。

```
例: 12345678-abcd-1234-abcd-1234567890ab
```

### よくあるエラー

❌ `A parameter cannot be found that matches parameter name 'Interactive'.`
→ `-Interactive`パラメータは不要。デフォルトで対話型動作する。

---

## SharePointサイトへの接続

### コマンド

```powershell
Connect-PnPOnline -Url "https://テナント名.sharepoint.com/sites/サイト名" -Interactive -ClientId "登録時に取得したClient ID"
```

### 各パラメータの意味

| パラメータ | 説明 |
|-----------|------|
| `-Url` | 接続先SharePointサイトのURL |
| `-Interactive` | ブラウザで対話的にサインインする |
| `-ClientId` | 初回セットアップで取得したClient ID |

### 接続確認

```powershell
Get-PnPWeb
```

サイトのタイトルやURLが表示されれば接続成功。

---

## 対象ページの確認

### サイトページライブラリの内部名を確認

日本語サイトでは「Site Pages」ではなく **「サイトのページ」** という名前になっている場合が多いです。

```powershell
Get-PnPList | Where-Object { $_.BaseTemplate -eq 119 } | Select-Object Title
```

**`BaseTemplate = 119`** がサイトページライブラリを示します。

### 全リスト一覧の確認

```powershell
Get-PnPList | Select-Object Title, BaseTemplate, Url | Format-Table -AutoSize
```

### ページ件数と内訳の確認

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500

$normal = 0
$draftNews = 0
$publishedNews = 0
$other = 0

foreach ($page in $pages) {
    switch ($page.FieldValues.PromotedState) {
        0 { $normal++ }
        1 { $draftNews++ }
        2 { $publishedNews++ }
        default { $other++ }
    }
}

Write-Host "=== ページ内訳 ===" -ForegroundColor Cyan
Write-Host "通常ページ      : $normal"
Write-Host "下書きニュース  : $draftNews"
Write-Host "公開済みニュース: $publishedNews"
Write-Host "その他          : $other"
Write-Host "合計            : $($pages.Count)" -ForegroundColor Yellow
```

### PromotedStateの意味

| 値 | 意味 |
|----|------|
| 0 | 通常のサイトページ |
| 1 | 下書き状態のニュース |
| 2 | 公開済みのニュース |

### コマンド解説

- `Get-PnPListItem -List "リスト名" -PageSize 500`: 指定リストのアイテムを最大500件ずつ取得
- `$page.FieldValues.PromotedState`: ページのニュース昇格状態を取得
- `$page.FieldValues.FileLeafRef`: ファイル名を取得
- `$page.FieldValues.FileRef`: サーバー相対URLを取得

---

## テスト実行（推奨）

本番前に**必ず2件程度でテスト**することを推奨します。

```powershell
# 最初の2ページだけテスト実行
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500
$testPages = $pages | Select-Object -First 2

Write-Host "=== テスト実行: 2ページのみ ===" -ForegroundColor Cyan

foreach ($page in $testPages) {
    $pageName = $page.FieldValues.FileLeafRef
    
    try {
        $clientPage = Get-PnPPage -Identity $pageName -ErrorAction Stop
        $clientPage.Publish()
        Write-Host "✓ 公開成功: $pageName" -ForegroundColor Green
    }
    catch {
        Write-Host "✗ 公開失敗: $pageName - $($_.Exception.Message)" -ForegroundColor Red
    }
}
```

### テスト後の確認ポイント

1. SharePointサイトの「サイトのページ」ライブラリを開く
2. テスト対象のページの「**更新日時**」が現在時刻に変わっているか
3. 「**更新者**」が自分のアカウントになっているか

---

## 本番実行：一括再公開

### スクリプト

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500

$success = 0
$failed = 0
$errors = @()
$total = $pages.Count
$current = 0

Write-Host "=== 一括再公開を開始: $total ページ ===" -ForegroundColor Cyan
$startTime = Get-Date

foreach ($page in $pages) {
    $current++
    $pageName = $page.FieldValues.FileLeafRef
    
    # 進捗バー表示
    Write-Progress -Activity "ページ再公開中" `
        -Status "$current / $total : $pageName" `
        -PercentComplete (($current / $total) * 100)
    
    try {
        $clientPage = Get-PnPPage -Identity $pageName -ErrorAction Stop
        $clientPage.Publish()
        $success++
    }
    catch {
        $failed++
        $errors += [PSCustomObject]@{
            PageName = $pageName
            Error = $_.Exception.Message
        }
        Write-Host "✗ 失敗: $pageName" -ForegroundColor Red
    }
}

Write-Progress -Activity "ページ再公開中" -Completed
$duration = (Get-Date) - $startTime

Write-Host "`n=== 完了 ===" -ForegroundColor Cyan
Write-Host "成功: $success 件" -ForegroundColor Green
Write-Host "失敗: $failed 件" -ForegroundColor Red
Write-Host "所要時間: $($duration.ToString('hh\:mm\:ss'))" -ForegroundColor Yellow

# エラーをCSV出力
if ($failed -gt 0) {
    $errors | Export-Csv -Path ".\PublishErrors.csv" -NoTypeInformation -Encoding UTF8
    Write-Host "エラー詳細: PublishErrors.csv" -ForegroundColor Yellow
}
```

### スクリプト解説

| 部分 | 役割 |
|------|------|
| `Get-PnPPage -Identity $pageName` | ページ名でページオブジェクトを取得 |
| `$clientPage.Publish()` | ページをメジャーバージョンとして公開 |
| `Write-Progress` | 進捗バーを表示（267件のような大量処理で便利） |
| `try-catch` | エラーが出ても処理を止めずに継続 |
| `Export-Csv` | 失敗したページのリストをCSV出力 |

### 所要時間の目安

- 100ページ: 約2〜3分
- 267ページ: 約6分
- 500ページ: 約10〜15分

---

## エラー対応

### CSVでエラー詳細を確認

```powershell
Import-Csv -Path ".\PublishErrors.csv" -Encoding UTF8 | Format-List
```

### 代表的なエラーと対処法

#### エラー1: JSONパースエラー

```
'c' is invalid after a value. Expected either ',', '}', or ']'.
```

**原因**: ファイル名やページ内の特殊文字（`=`、`!`、引用符など）が原因で、`Get-PnPPage`が内部的にJSONパースに失敗する。

**対処法**: `Get-PnPPage`ではなく**ファイル直接Publish**を使う：

```powershell
# 失敗したページを特定
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500
$targetPage = $pages | Where-Object { $_.FieldValues.FileLeafRef -like "*ファイル名の一部*" }

# サーバー相対URLを取得
$serverRelativeUrl = $targetPage.FieldValues.FileRef

# ファイルを直接Publish
$ctx = Get-PnPContext
$file = $ctx.Web.GetFileByServerRelativeUrl($serverRelativeUrl)
$ctx.Load($file)
Invoke-PnPQuery

Write-Host "現在のバージョン: $($file.UIVersionLabel)"
Write-Host "公開状態: $($file.Level)"

$file.Publish("Bulk republish")
Invoke-PnPQuery

Write-Host "✓ 公開成功!" -ForegroundColor Green
```

#### エラー2: アクセス拒否

```
Access denied. You do not have permission to perform this action.
```

**原因**: ページに対する編集権限がない。

**対処法**: SharePointサイトの設定で、対象ページの権限を確認・修正する。

#### エラー3: ファイルが見つからない

```
File Not Found.
```

**原因**: 既に削除されたページが取得されている、またはチェックアウト中。

**対処法**: ページが実在するか、誰かが編集中ではないかを確認。

---

## トラブルシューティング

### Q1. 「リスト 'Site Pages' は存在しません」エラー

**A**: 日本語サイトではリスト名が「サイトのページ」になっている。`BaseTemplate = 119`で正しい名前を確認する。

### Q2. Client IDが分からなくなった

**A**: Entra ID管理センター（[https://entra.microsoft.com](https://entra.microsoft.com)）の「アプリの登録」から「PnP PowerShell」を検索。アプリ登録の「概要」ページに**アプリケーション(クライアント)ID**として表示されている。

### Q3. 接続が切れた／タイムアウト

**A**: `Connect-PnPOnline`を再実行。長時間処理中の場合、PCをスリープさせないように注意。

### Q4. 「再公開」を取り消したい

**A**: ページごとのバージョン履歴から、過去のバージョンに戻すことは可能だが、267件すべてを一括で戻すのは困難。**作業前にバージョン履歴のバックアップを取ることを推奨**。

---

## 用語解説

| 用語 | 説明 |
|------|------|
| **PnP PowerShell** | Microsoftコミュニティ製のSharePoint操作用PowerShellモジュール。SharePoint Online管理の事実上の標準ツール。 |
| **Entra ID** | 旧Azure Active Directory。Microsoft 365のID管理サービス。 |
| **Client ID** | アプリ登録時に発行されるGUID。アプリケーションを一意に識別する。 |
| **Admin Consent** | アプリの権限について管理者が組織全体に対して同意を与えること。 |
| **再公開（Republish）** | ページの内容は変えずに、メジャーバージョンを上げて公開する操作。更新日時と更新者が現在の値に書き換わる。 |
| **PromotedState** | ページがニュースとして昇格されているかを示す内部状態（0=通常、1=下書きニュース、2=公開ニュース）。 |
| **BaseTemplate** | SharePointリストの種類を示す数値。119はサイトページライブラリ。 |
| **FileRef** | サイトルートからのサーバー相対URL（例: `/sites/SiteName/SitePages/Page.aspx`）。 |
| **メジャーバージョン** | 1.0、2.0、3.0…のように整数で上がるバージョン。公開時に進む。 |
| **マイナーバージョン** | 1.1、1.2、1.3…のような下書き保存時のバージョン。 |

---

## 再公開時の注意事項

一括再公開を実行する前に、以下を**必ず確認**してください：

### ⚠️ 影響範囲

1. **更新日時と更新者がすべて現在の値に書き換わる**（元に戻せません）
2. **メジャーバージョンが+1される**（例: 2.0 → 3.0）
3. **ニュースの並び順が崩れる可能性**
   - ホームページのニュースWebパーツが「更新日時順」の場合、すべてのニュースが今日付になり順序が変わる
4. **大量の更新通知が飛ぶ可能性**
   - フォロー・アラート設定をしているユーザーに通知が送られる
5. **承認ワークフローがある場合、再承認待ちになる**

### 推奨される事前作業

- [ ] 重要なページのバージョン履歴を確認
- [ ] 関係者に作業実施を事前通知
- [ ] 業務時間外に実施（通知の影響を最小化）
- [ ] 必ず2件程度でテスト実行してから本番

---

## 完全な実行フロー（コピペ用）

```powershell
# === 1. 接続 ===
Connect-PnPOnline -Url "https://テナント名.sharepoint.com/sites/サイト名" -Interactive -ClientId "Client ID"

# === 2. リスト名確認 ===
Get-PnPList | Where-Object { $_.BaseTemplate -eq 119 } | Select-Object Title

# === 3. 対象件数確認 ===
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500
Write-Host "対象ページ数: $($pages.Count)"

# === 4. テスト実行（2件） ===
$testPages = $pages | Select-Object -First 2
foreach ($page in $testPages) {
    $pageName = $page.FieldValues.FileLeafRef
    try {
        (Get-PnPPage -Identity $pageName).Publish()
        Write-Host "✓ $pageName" -ForegroundColor Green
    } catch {
        Write-Host "✗ $pageName - $($_.Exception.Message)" -ForegroundColor Red
    }
}

# === 5. 本番実行（全件） ===
$success = 0; $failed = 0; $errors = @()
$total = $pages.Count; $current = 0
$startTime = Get-Date

foreach ($page in $pages) {
    $current++
    $pageName = $page.FieldValues.FileLeafRef
    Write-Progress -Activity "再公開中" -Status "$current/$total : $pageName" -PercentComplete (($current/$total)*100)
    try {
        (Get-PnPPage -Identity $pageName -ErrorAction Stop).Publish()
        $success++
    } catch {
        $failed++
        $errors += [PSCustomObject]@{ PageName = $pageName; Error = $_.Exception.Message }
    }
}

Write-Progress -Activity "再公開中" -Completed
$duration = (Get-Date) - $startTime
Write-Host "成功: $success / 失敗: $failed / 所要時間: $($duration.ToString('hh\:mm\:ss'))"

if ($failed -gt 0) {
    $errors | Export-Csv -Path ".\PublishErrors.csv" -NoTypeInformation -Encoding UTF8
}

# === 6. 切断 ===
Disconnect-PnPOnline
```

---

## 参考リンク

- [PnP PowerShell 公式ドキュメント](https://pnp.github.io/powershell/)
- [Get-PnPPage コマンドレット](https://pnp.github.io/powershell/cmdlets/Get-PnPPage.html)
- [Connect-PnPOnline コマンドレット](https://pnp.github.io/powershell/cmdlets/Connect-PnPOnline.html)
- [Register-PnPEntraIDAppForInteractiveLogin](https://pnp.github.io/powershell/cmdlets/Register-PnPEntraIDAppForInteractiveLogin.html)

---

## 作業実績ログ

| 日付 | 対象サイト | 件数 | 成功 | 失敗 | 所要時間 | 備考 |
|------|-----------|------|------|------|----------|------|
| 2026-04-30 | NewsPages | 267 | 267 | 0 | 約6分 | 1件はファイル直接Publishでリカバリー |

---

*最終更新: 2026年4月30日*
