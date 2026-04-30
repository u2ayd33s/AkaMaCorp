---
date: 2026-04-30
tags:
  - agent/sharepoint
  - type/📖how-to
  - type/📚reference
  - project/sharepoint
---

# SharePoint ページ・ニュース 一括再公開マニュアル

## 概要

PnP PowerShell を使って、SharePoint サイト内のすべてのページ（通常ページ＋ニュース）を一括で再公開する手順をまとめたナレッジ。

**実績**: 267ページを約6分で再公開完了（2026年4月実施）

---

## 内容

### 1. 事前準備

#### 必要な環境

- Windows PowerShell 5.1 または PowerShell 7.x
- PnP PowerShell モジュール
- SharePoint サイトへのアクセス権限（最低でも編集者権限）
- Entra ID（旧 Azure AD）でアプリ登録できる権限（初回のみ）

#### PnP PowerShell のインストール

```powershell
Install-Module -Name PnP.PowerShell -Scope CurrentUser
```

---

### 2. 初回セットアップ：Entra ID アプリ登録

PnP PowerShell 2.x 以降では、自前の Client ID が必須。**この作業は初回のみ**。

```powershell
Register-PnPEntraIDAppForInteractiveLogin -ApplicationName "PnP PowerShell" -Tenant "テナント名.onmicrosoft.com"
```

| パラメータ | 説明 |
|-----------|------|
| `-ApplicationName` | Entra ID に登録するアプリの表示名（任意） |
| `-Tenant` | テナント名。`xxx.onmicrosoft.com` の形式 |

**実行の流れ**:

1. ブラウザでサインイン画面が表示される
2. **グローバル管理者または特権ロール管理者**でサインイン
3. アプリの権限について同意（Admin Consent）
4. Client ID（GUID）が表示される

> ⚠️ **重要**: 表示された Client ID は必ずメモすること。次回以降の接続で必要。

---

### 3. SharePoint サイトへの接続

```powershell
Connect-PnPOnline -Url "https://テナント名.sharepoint.com/sites/サイト名" -Interactive -ClientId "登録時に取得したClient ID"
```

接続確認:

```powershell
Get-PnPWeb
```

---

### 4. 対象ページの確認

#### サイトページライブラリの内部名を確認

日本語サイトでは「Site Pages」ではなく **「サイトのページ」** になっている場合が多い。

```powershell
Get-PnPList | Where-Object { $_.BaseTemplate -eq 119 } | Select-Object Title
```

**`BaseTemplate = 119`** がサイトページライブラリ。

#### ページ件数と内訳の確認

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500

$normal = 0; $draftNews = 0; $publishedNews = 0; $other = 0

foreach ($page in $pages) {
    switch ($page.FieldValues.PromotedState) {
        0 { $normal++ }
        1 { $draftNews++ }
        2 { $publishedNews++ }
        default { $other++ }
    }
}

Write-Host "通常ページ      : $normal"
Write-Host "下書きニュース  : $draftNews"
Write-Host "公開済みニュース: $publishedNews"
Write-Host "合計            : $($pages.Count)"
```

#### PromotedState の意味

| 値 | 意味 |
|----|------|
| 0 | 通常のサイトページ |
| 1 | 下書き状態のニュース |
| 2 | 公開済みのニュース |

---

### 5. テスト実行（推奨）

本番前に**必ず2件程度でテスト**する。

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500
$testPages = $pages | Select-Object -First 2

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

#### テスト後の確認ポイント

1. SharePoint の「サイトのページ」ライブラリを開く
2. テスト対象ページの「**更新日時**」が現在時刻に変わっているか
3. 「**更新者**」が自分のアカウントになっているか

---

### 6. 本番実行：一括再公開

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500

$success = 0; $failed = 0; $errors = @()
$total = $pages.Count; $current = 0
$startTime = Get-Date

foreach ($page in $pages) {
    $current++
    $pageName = $page.FieldValues.FileLeafRef

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
    }
}

Write-Progress -Activity "ページ再公開中" -Completed
$duration = (Get-Date) - $startTime

Write-Host "成功: $success 件" -ForegroundColor Green
Write-Host "失敗: $failed 件" -ForegroundColor Red
Write-Host "所要時間: $($duration.ToString('hh\:mm\:ss'))"

if ($failed -gt 0) {
    $errors | Export-Csv -Path ".\PublishErrors.csv" -NoTypeInformation -Encoding UTF8
}
```

#### 所要時間の目安

| 件数 | 所要時間 |
|------|---------|
| 100ページ | 約2〜3分 |
| 267ページ | 約6分 |
| 500ページ | 約10〜15分 |

---

### 7. エラー対応

#### エラー1: JSON パースエラー

```
'c' is invalid after a value. Expected either ',', '}', or ']'.
```

**原因**: ファイル名やページ内の特殊文字（`=`、`!`、引用符など）が原因で、`Get-PnPPage` が内部的に JSON パースに失敗する。

**対処法**: `Get-PnPPage` ではなく**ファイル直接 Publish** を使う。

```powershell
$pages = Get-PnPListItem -List "サイトのページ" -PageSize 500
$targetPage = $pages | Where-Object { $_.FieldValues.FileLeafRef -like "*ファイル名の一部*" }

$serverRelativeUrl = $targetPage.FieldValues.FileRef

$ctx = Get-PnPContext
$file = $ctx.Web.GetFileByServerRelativeUrl($serverRelativeUrl)
$ctx.Load($file)
Invoke-PnPQuery

$file.Publish("Bulk republish")
Invoke-PnPQuery
```

#### エラー2: アクセス拒否

```
Access denied. You do not have permission to perform this action.
```

**対処法**: SharePoint サイトの設定で、対象ページの権限を確認・修正。

#### エラー3: ファイルが見つからない

```
File Not Found.
```

**対処法**: ページが実在するか、誰かが編集中（チェックアウト中）でないかを確認。

---

### 8. ⚠️ 再公開時の注意事項

実行前に**必ず確認**:

1. **更新日時と更新者がすべて現在の値に書き換わる**（元に戻せない）
2. **メジャーバージョンが +1 される**（例: 2.0 → 3.0）
3. **ニュースの並び順が崩れる可能性**（ホームの Web パーツが「更新日時順」の場合）
4. **大量の更新通知が飛ぶ可能性**（フォロー・アラート設定ユーザーへ）
5. **承認ワークフローがある場合、再承認待ちになる**

#### 推奨される事前作業

- [ ] 重要なページのバージョン履歴を確認
- [ ] 関係者に作業実施を事前通知
- [ ] 業務時間外に実施（通知の影響を最小化）
- [ ] 必ず2件程度でテスト実行してから本番

---

### 9. 用語解説

| 用語 | 説明 |
|------|------|
| **PnP PowerShell** | Microsoft コミュニティ製の SharePoint 操作用 PowerShell モジュール |
| **Entra ID** | 旧 Azure Active Directory。Microsoft 365 の ID 管理サービス |
| **Client ID** | アプリ登録時に発行される GUID |
| **Admin Consent** | アプリの権限について管理者が組織全体に同意を与えること |
| **再公開（Republish）** | ページの内容は変えずに、メジャーバージョンを上げて公開する操作 |
| **PromotedState** | ニュース昇格状態（0=通常、1=下書きニュース、2=公開ニュース） |
| **BaseTemplate** | SharePoint リストの種類を示す数値。119 はサイトページライブラリ |
| **FileRef** | サイトルートからのサーバー相対 URL |
| **メジャーバージョン** | 1.0、2.0、3.0… のように整数で上がるバージョン |
| **マイナーバージョン** | 1.1、1.2、1.3… のような下書き保存時のバージョン |

---

## 作業実績ログ

| 日付 | 対象サイト | 件数 | 成功 | 失敗 | 所要時間 | 備考 |
|------|-----------|------|------|------|----------|------|
| 2026-04-30 | NewsPages | 267 | 267 | 0 | 約6分 | 1件はファイル直接 Publish でリカバリー |

---

## 参考リンク

- [PnP PowerShell 公式ドキュメント](https://pnp.github.io/powershell/)
- [Get-PnPPage コマンドレット](https://pnp.github.io/powershell/cmdlets/Get-PnPPage.html)
- [Connect-PnPOnline コマンドレット](https://pnp.github.io/powershell/cmdlets/Connect-PnPOnline.html)
- [Register-PnPEntraIDAppForInteractiveLogin](https://pnp.github.io/powershell/cmdlets/Register-PnPEntraIDAppForInteractiveLogin.html)
- [Microsoft Entra 管理センター](https://entra.microsoft.com)

---

## 関連

- [[]] <!-- SharePoint 関連の他ナレッジを追加予定 -->
- AkaMaCorp エージェント: `05_sharepoint`
- 関連スキル: `sp-rest-api`, `sp-permissions`, `ms-graph-api`
