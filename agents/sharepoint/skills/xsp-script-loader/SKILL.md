---
name: xsp-script-loader
description: Artisan X-SP ScriptLoader（SPFx Application Customizer）を使ったSharePointカスタマイズ開発スキル。"スクリプトローダー", "ScriptLoader", "ページにJS/CSSを読み込む", "SiteAssets/ScriptLoader", "global.js", "ページ固有のスタイル", "SharePointでカスタムデザイン", "SPFxなしでカスタマイズ", "ページに処理を追加したい", "SharePointの見た目を変えたい" などが出たら積極的に使用する。SharePoint標準機能では実装できないデザイン・機能を、ページパスに対応したJS/CSSで実装するパターンをカバーする。
---

# xsp-script-loader 開発

SPFx Application Customizer「xsp-script-loader」を通じて、SharePoint の各ページに JS/CSS を自動注入するカスタマイズ開発を支援します。

詳細な仕様は `references/ScriptLoader-DevelopmentManual.md` を参照してください。
パッケージファイルは `../../../xsp-script-loader/` に格納されています。

---

## 仕組みの概要

```
SharePoint サイト
  └─ SiteAssets/ScriptLoader/    ← ここにファイルを置くだけで自動読み込み
       ├── global.js              全ページ共通
       ├── global.css
       ├── SitePages__.js         SitePages 配下すべて
       ├── SitePages__.css
       ├── SitePages__Category1__.js   特定フォルダ配下
       └── SitePages__top.js     特定ページのみ (SitePages/top.aspx)
```

xsp-script-loader（SPFx Application Customizer）が各ページ表示時にページパスを解析し、対応するファイルを `<script>` / `<link>` タグとして動的に挿入します。

---

## ファイル命名規則

| ファイル名 | 読み込まれるページ |
|-----------|-----------------|
| `global.js` / `global.css` | サイト全ページ |
| `SitePages__.js` / `SitePages__.css` | SitePages 配下の全ページ |
| `SitePages__フォルダ名__.js` | SitePages/フォルダ名/ 配下の全ページ |
| `SitePages__ページ名.js` | SitePages/ページ名.aspx のみ |
| `SitePages__フォルダ名__ページ名.js` | SitePages/フォルダ名/ページ名.aspx のみ |
| `SitePages2__.js` | 別ページライブラリ (SitePages2) 配下すべて |

**読み込み順（累積）**: global → ライブラリ全体 → フォルダ → 特定ページ の順で重ねて読み込まれます。

---

## JS 記述パターン

### 基本構造（部分更新対応）
SharePoint は SPA 的な部分更新でページ遷移するため、`window.registerSpLoaderFire()` で登録しないと遷移後に処理が走りません。

```javascript
(() => {
  // ページ表示・遷移のたびに実行される処理はここに登録
  window.registerSpLoaderFire(loaderFire);

  function loaderFire(event) {
    // メイン処理
    renderCustomContent();
  }

  function renderCustomContent() {
    // DOM 操作・API 呼び出しなど
  }
})();
```

### ロードイベントの安全な書き方
```javascript
// ページ読み込み完了チェック（必須）
if (document.readyState === 'complete') {
  onLoadHandler();
} else {
  window.addEventListener('load', onLoadHandler);
}

function onLoadHandler() {
  // 処理
}
```

### ページコンテキスト・ユーザー情報の取得
```javascript
// サイト URL
const siteUrl = window?.SPLoader?.PageContext?.web?.absoluteUrl;

// 現在のページ URL
const pageUrl = window?.SPLoader?.PageContext?.legacyPageContext?.serverRequestPath;

// ログインユーザーのメールアドレス
const email = window?.SPLoader?.UserProperties?.Email;

// ログインユーザーの表示名
const displayName = window?.SPLoader?.UserProperties?.DisplayName;
```

### SharePoint REST API との組み合わせ
```javascript
window.registerSpLoaderFire(loaderFire);

async function loaderFire() {
  const siteUrl = window?.SPLoader?.PageContext?.web?.absoluteUrl;

  // リストアイテム取得
  const res = await fetch(`${siteUrl}/_api/web/lists/getbytitle('お知らせ')/items?$select=Title,Body,Created&$orderby=Created desc&$top=5`, {
    headers: { 'Accept': 'application/json;odata=verbose' }
  });
  const data = await res.json();
  renderNews(data.d.results);
}

function renderNews(items) {
  const container = document.querySelector('#custom-news');
  if (!container) return;
  container.innerHTML = items.map(item => `
    <article class="news-item">
      <h3>${item.Title}</h3>
      <p>${item.Body ?? ''}</p>
      <time>${new Date(item.Created).toLocaleDateString('ja-JP')}</time>
    </article>
  `).join('');
}
```

---

## CSS 記述のポイント

SharePoint の標準スタイルと競合しないよう、スコープを絞ったセレクタを使います。

```css
/* ❌ 広すぎるセレクタ（標準 UI を壊す可能性）*/
h2 { color: red; }

/* ✅ カスタム要素にクラスを付けてスコープを限定 */
.custom-news-feed h2 { color: #0078d4; }

/* ✅ SharePoint のテーマ変数を活用 */
.custom-card {
  background: var(--neutralLighterAlt, #f3f2f1);
  color: var(--neutralPrimary, #323130);
}
```

---

## ファイルアップロード手順

1. SharePoint サイトを開く
2. 歯車 → 「サイトコンテンツ」→「サイトのリソースファイル」→「ScriptLoader」フォルダを開く
3. 作成した `.js` / `.css` ファイルをアップロード
4. 対象ページを開いて動作確認

---

## デバッグ方法

| 方法 | 手順 |
|------|------|
| デバッグログ表示 | URL の末尾に `?debug=console` を追加 → DevTools Console を確認 |
| ブレークポイント | F12 → ソースタブ → SiteAssets/ScriptLoader 配下のファイルを開く |
| キャッシュクリア | 強制リフレッシュ (Ctrl+Shift+R) または 10 分待つ |

> **キャッシュ注意**: ファイル更新後、最大 10 分間はブラウザキャッシュが使われます（タイムスタンプは 10 分単位で更新）。

---

## 作業フロー

```
1. 対象ページ・スコープを確認する
   → 全ページ? SitePages 全体? 特定ページのみ?

2. ファイル名を決定する（命名規則に従う）

3. JS/CSS を実装する
   - JS: window.registerSpLoaderFire() で部分更新に対応
   - CSS: スコープを絞ったセレクタで標準 UI を守る

4. SiteAssets/ScriptLoader にアップロードする

5. 対象ページで動作確認する
   - ?debug=console でログ確認
   - 複数ページで意図した範囲にのみ読み込まれているか確認
```

---

## パッケージ情報（xsp-script-loader）

| 項目 | 値 |
|------|-----|
| 製品名 | Artisan X-SP ScriptLoader |
| バージョン | 1.5.0 |
| Component ID | `fb680a2e-77cf-44c5-804a-ca2fa3764e9d` |
| Product ID | `3bef2208-7908-4438-b70e-cebeba7219d1` |
| デプロイ方式 | テナント全体展開 (SkipFeatureDeployment=true) |
| パッケージパス | `agents/sharepoint/xsp-script-loader/` |

---

## 更新履歴
- v1.0: 初版作成（ScriptLoader-DevelopmentManual.md を元に構成）
