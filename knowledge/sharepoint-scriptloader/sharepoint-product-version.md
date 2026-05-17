# SharePoint 製品バージョン確認ガイド

## 1. buildversion.cnf の読み方

URL にアクセスするだけで確認できる最も簡単な方法。

```
https://<テナント>.sharepoint.com/_vti_pvt/buildversion.cnf
```

### レスポンス例

```
vti_encoding:SR|utf8-nl
vti_buildversion:SR|16.0.27306.12004
```

### 各フィールドの意味

| フィールド | 値 | 意味 |
|---|---|---|
| `vti_encoding` | `utf8-nl` | VTI メタデータ形式、UTF-8 エンコーディング |
| `vti_buildversion` | `16.0.27306.12004` | SharePoint のビルドナンバー |

### ビルドナンバーの構造

```
16   .  0   .  27306  .  12004
↑       ↑      ↑          ↑
メジャー マイナー ビルド番号   リビジョン番号
(固定)  (固定)  (機能単位)   (パッチ単位)
```

| セグメント | 値 | 説明 |
|---|---|---|
| `16` | メジャーバージョン | SharePoint 2016世代の固定値（SPO も同じ） |
| `0` | マイナーバージョン | 常に 0（実質的な意味なし） |
| `27306` | **ビルド番号** | 機能リリースの識別番号。更新のたびに上昇する |
| `12004` | **リビジョン番号** | パッチ・修正の細かいレベル |

> **補足：** SharePoint Online は `16.0.x.x` が固定。オンプレミス時代の世代番号（SP2013=15、SP2016/SP2019=16）がそのまま踏襲されている。

---

## 2. ビルドバージョン確認方法一覧

### ① buildversion.cnf（ブラウザで直接確認）

```
https://<テナント>.sharepoint.com/_vti_pvt/buildversion.cnf
```

- ✅ 管理者権限不要
- ✅ 最も手軽

### ② REST API

```
https://<テナント>.sharepoint.com/_api/web
```

- ✅ JSON/XML で詳細なサイト情報も取得可能
- ✅ SPFx・スクリプト開発での利用に適する

### ③ PnP PowerShell

```powershell
Connect-PnPOnline -Url "https://<テナント>.sharepoint.com" -Interactive
Get-PnPSite | Select-Object Url, CompatibilityLevel
```

### ④ SharePoint 管理センター（UI）

```
https://<テナント>-admin.sharepoint.com
```

右上「?（ヘルプ）」→「バージョン情報」

---

## 3. システム更新の確認方法

SharePoint Online はクラウドのため、Microsoft が自動的にローリングアップデートを実施する。  
ビルドナンバーより「**何がいつ展開されるか**」を追う仕組みが重要。

### 確認先まとめ

| 目的 | 確認先 | URL |
|---|---|---|
| 今後のアップデート予定 | Microsoft 365 ロードマップ | https://www.microsoft.com/en-us/microsoft-365/roadmap?filters=SharePoint |
| テナントへの通知・告知 | M365 管理センター → メッセージ センター | https://admin.microsoft.com |
| 自テナントの設定変更履歴 | SharePoint 管理センター → 変更履歴 | https://<テナント>-admin.sharepoint.com |
| 障害・メンテナンス情報 | M365 管理センター → サービス正常性 | https://admin.microsoft.com |
| 新機能情報 | SharePoint コミュニティブログ | https://techcommunity.microsoft.com/category/sharepoint |

### ① Microsoft 365 ロードマップ

ステータスは3段階：

| ステータス | 意味 |
|---|---|
| **In development** | 開発中（まだ展開されていない） |
| **Rolling out** | 展開中（テナントに届きつつある） |
| **Launched** | 展開完了 |

### ② メッセージ センター（管理者向け）

`SharePoint Online` でフィルタリングし、以下のタグで管理：

- `Feature update` — 機能アップデート
- `Major update` — メジャーアップデート（30日前通知）
- `New feature` — 新機能
- `Retirement` — 廃止予定

### ③ 変更履歴レポート（SharePoint 管理センター）

- 対象：過去 **180日以内** のサイト設定・組織設定の変更
- 出力：CSV レポート（最大10件作成可能）
- 確認できる内容：何が変更されたか・いつ・誰が実行したか

```
SharePoint 管理センター → レポート → 変更履歴 → レポートの作成
```

---

## 4. リリースオプション（早期アクセス）

| オプション | 説明 |
|---|---|
| **Standard release**（標準） | 全テナントに一般展開されるタイミングで受け取る（デフォルト） |
| **Targeted release**（対象指定） | 他テナントより先に最新アップデートを受け取る |

設定場所：
```
Microsoft 365 管理センター → 設定 → 組織設定 → リリース設定
```

---

## 5. ビルドナンバーの実用的な使いどころ

日常的な管理には不要だが、以下の場面で活用する：

1. **Microsoft サポートへの問い合わせ時**  
   「ビルド `16.0.27306.12004` で〇〇の挙動が発生しています」

2. **ロードマップとの照合**  
   「Rolling out になっている機能がテナントにまだ来ていない」場合の根拠情報

3. **SPFx 開発での互換性確認**  
   特定のビルド以降でのみ動作する API を使用する際の確認

---

## 6. SPO と SharePoint Server のバージョン体系比較

| | SharePoint Online | SharePoint Server |
|---|---|---|
| バージョン体系 | `16.0.x.x`（固定） | `15.x`（2013）/ `16.x`（2016/2019/SE） |
| 更新方式 | Microsoft が自動ローリングアップデート | 管理者が CU（累積更新）を手動適用 |
| ビルド管理 | 不要（Microsoft が管理） | 必要（CU 適用状況を管理） |
| バージョン確認の重要度 | 低（サポート問い合わせ時のみ） | 高（日常的なパッチ管理が必要） |
