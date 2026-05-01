---
name: SitePages__xspNewsPageSwitchTabs-v2 実装内容
description: SharePointのサイトのページをカテゴリタブ・検索・タイル/リスト表示で一覧表示するScript Loaderスクリプトの実装詳細
type: project
originSessionId: 1116c846-505a-4a24-bb90-0453cca298f9
---
## ファイル
`src/SitePages__xspNewsPageSwitchTabs-v2.js`

## 概要
SharePoint Script Loader (Application Customizer) 経由で注入するIIFEベースのJS。
`div[data-automation-id="textBox"]` 内にトリガー文言 `xspNewsPageSwitchTabs` を検出して起動。

## 設定
- siteUrl: `https://u2ayd33s.sharepoint.com/sites/NewsPages`
- エンドポイント: `/_api/web/lists/getByTitle('サイトのページ')/RenderListDataAsStream` (POST)
- カテゴリマスタ: `/_api/web/lists/GetByTitle('NewsMasteList')/items?$select=ID,Title,OrderBy&$orderby=OrderBy asc`

## フィールド取得の注意点
- RenderListDataAsStream はOData REST APIと**フィールド名が異なる**
- ルックアップ列 `xspTabCategory` は `"{ID}_{Title}"` 形式で返る（例: `"4_ツール"`）
- IDは `tabCatRaw.split('_')[0]` で抽出する
- `TabCategoryId` フィールドは undefined になるため使用しない

## フィルタ要件
- `home.aspx` / `dashboard.aspx` は非表示
- `xspTabCategory` が空のアイテムは非表示
- CAML ViewXml で `PromotedState != 1` をサーバーサイドフィルタ
- 表示順: `FirstPublishedDate → Created` 降順

## 削除・廃止した機能
- `isNew` プロパティ（タブ配列）→ 不要で削除
- `新着` タブ (`id: 'new'`) → `すべて` タブが日付降順のため重複、削除
- `isWithinDays()` / `CFG.newsDays` → 新着タブ廃止に伴い削除
- `nst-tab-dot` / `--nst-green` / `--nst-green-bright` → 削除
- `nst-hero` セクション → 削除済み

## カラー設計
Fluent UI 3 CSS変数（`.fui-FluentProvider3`）をすべてのカラー・シャドウに使用。
ハードコードカラー (#hex / rgba) はゼロ。テーマ変更に自動追従する。
主なトークンマッピング:
- `--nst-ink` → `var(--colorNeutralForeground1)`
- `--nst-tint` → `var(--colorBrandForeground1)`
- `--nst-tint-bright` → `var(--colorBrandBackground)`
- `--nst-tint-deep` → `var(--colorBrandBackgroundSelected)`
- `--nst-shadow-card` → `var(--shadow2)`
- `--nst-shadow-hover` → `var(--shadow16)`
- `--nst-shadow-panel` → `var(--shadow28)`

## ブレイクポイント
- Default (〜575px): 1カラム・コンパクト
- `576px` (モバイル): パネルpadding増、グリッド2カラム
- `768px` (タブレット): グリッド3カラム・リスト行フル表示・chevron表示
- `1024px` (PC): グリッド4カラム・デスクトップパネルレイアウト

## パネルレイアウト
**PC (1024px+)**: 1行 — タブ | 検索(200px固定) | 表示切替 | 投稿ボタン
**モバイル (<1024px)**:
- Row1: タブ ＋ アコーディオンボタン（シェブロン▼）
- Row2（アコーディオン折りたたみ）: 検索 ＋ 表示切替
- 投稿ボタンはモバイルでは非表示（PC専用）

## アコーディオン実装
- ボタン: `[data-nst-accordion]`、ボディ: `[data-nst-accordion-body]`
- CSSは `max-height: 0→80px` + `opacity` トランジション
- `.open` クラスで SVG chevron が `rotate(180deg)`

## デバッグログ
すべての `console.log/group/warn`（診断用）は削除済み。
残存: `console.error`（フェッチエラー）と `console.warn`（マウント失敗）のみ。
