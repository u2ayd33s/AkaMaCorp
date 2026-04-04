---
name: sp-html-viewer
description: sp-html-viewer Web パーツ向けの HTML ファイルを作成するスキル。"sp-html-viewer", "HTMLビューアー", "SPFxでHTMLを表示", "ドキュメントライブラリのHTML" などのキーワードで使用する。Web パーツの仕様・表示モード・SharePoint スタイル競合の回避・レイアウト安定化を考慮した HTML/CSS/JS を生成する。
---

# sp-html-viewer HTML 作成マニュアル

Artisan Inc. 製 SPFx Web パーツ「sp-html-viewer」(v0.1.2) 向けの HTML ファイルを効率的に作成するためのマニュアル。

---

## Web パーツの仕組み

1. SharePoint ドキュメントライブラリに **HTML ファイル**をアップロードする
2. sp-html-viewer Web パーツを SharePoint ページに配置する
3. プロパティペインでライブラリ・HTML ファイルを選択する
4. HTML が Web パーツ内（または全画面）に表示される

### 表示モード

| モード | 説明 | 使いどころ |
|--------|------|-----------|
| `embed`（埋め込み） | Web パーツ枠内に表示 | ページの一部として組み込む場合 |
| `fullscreen`（全画面） | SharePoint UI を隠して全画面表示 | 専用ツール・ダッシュボードとして使う場合 |

### 全画面モードで非表示にできる UI 要素

| 設定項目 | 対象 |
|---------|------|
| 最上位ヘッダー | Microsoft 365 のスイートナビ（上部黒帯） |
| サイトヘッダー | サイト名・ロゴ・グローバルナビ |
| サイドナビゲーション | 左側のナビゲーション |
| アプリバー | 左端の縦型アプリランチャー |
| ソーシャルバー | ページ下部のいいね・コメントエリア |

---

## HTML ファイルの基本構造

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ページタイトル</title>
  <style>
    /* リセット: SharePoint のスタイルを上書き */
    *, *::before, *::after { box-sizing: border-box; }

    body {
      margin: 0;
      padding: 0;
      font-family: "Segoe UI", "Yu Gothic UI", sans-serif;
      font-size: 14px;
      color: #201f1e;
      background: #ffffff;
    }

    /* メインコンテナ */
    .sp-page {
      max-width: 1200px;
      margin: 0 auto;
      padding: 24px;
    }
  </style>
</head>
<body>
  <div class="sp-page">
    <!-- コンテンツここから -->

    <!-- コンテンツここまで -->
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', function() {
      // JavaScript はここに記述
    });
  </script>
</body>
</html>
```

---

## CSS 設計のポイント

### 1. クラス名にプレフィックスをつける

SharePoint のグローバルスタイルと競合しないよう、独自プレフィックスを使う。

```css
/* NG: 汎用クラス名 */
.button { ... }
.card { ... }

/* OK: プレフィックスあり */
.mc-button { ... }
.mc-card { ... }
```

### 2. CSS カスタムプロパティでデザイントークンを管理

```css
:root {
  /* ブランドカラー */
  --mc-primary:       #0078d4;
  --mc-primary-dark:  #005a9e;
  --mc-primary-light: #c7e0f4;

  /* テキスト */
  --mc-text:          #201f1e;
  --mc-text-sub:      #605e5c;

  /* 背景・ボーダー */
  --mc-bg:            #ffffff;
  --mc-bg-gray:       #f3f2f1;
  --mc-border:        #edebe9;

  /* スペーシング */
  --mc-space-sm: 8px;
  --mc-space-md: 16px;
  --mc-space-lg: 24px;
  --mc-space-xl: 32px;

  /* フォント */
  --mc-font: "Segoe UI", "Yu Gothic UI", sans-serif;
}
```

### 3. 埋め込みモード時の高さ対応

埋め込みモードでは Web パーツの高さが自動調整される。コンテンツ量に応じて高さが変わるため、`height: 100vh` は使わない。

```css
/* NG: 埋め込みモードで意図しない高さになる */
.container { height: 100vh; }

/* OK: コンテンツに合わせた高さ */
.container { min-height: 400px; }
```

### 4. position: sticky の注意

Web パーツ内では `position: sticky` が効かない場合がある。
プロパティペインの「互換モード → position: sticky を無効化」が ON だと完全に無効になる。

```css
/* sticky が必要な場合は position: fixed で代替する場合も */
.mc-header {
  position: sticky; /* 無効化されている場合は fixed に切り替え */
  top: 0;
  z-index: 100;
  background: #fff;
}
```

---

## JavaScript のポイント

### 基本パターン

```javascript
document.addEventListener('DOMContentLoaded', function() {
  // DOM 操作はここから
  init();
});

function init() {
  // 初期化処理
}
```

### SharePoint REST API を呼ぶ場合

HTML ファイルは SharePoint 上で動作するため、同一テナントの REST API を直接呼べる。

```javascript
async function getRequestDigest() {
  const res = await fetch('/_api/contextinfo', {
    method: 'POST',
    headers: { 'Accept': 'application/json;odata=nometadata' }
  });
  const data = await res.json();
  return data.FormDigestValue;
}

async function getListItems(listTitle) {
  const res = await fetch(
    `/sites/{サイト名}/_api/web/lists/getbytitle('${listTitle}')/items?$top=100`,
    { headers: { 'Accept': 'application/json;odata=nometadata' } }
  );
  const data = await res.json();
  return data.value;
}
```

### 現在のユーザー情報を取得

```javascript
async function getCurrentUser() {
  const res = await fetch(
    '/_api/web/currentuser?$select=Title,Email,LoginName',
    { headers: { 'Accept': 'application/json;odata=nometadata' } }
  );
  return res.json();
}
```

### エラーハンドリング

```javascript
async function safeGetListItems(listTitle) {
  try {
    return await getListItems(listTitle);
  } catch (e) {
    console.error('リスト取得失敗:', e);
    showError('データの取得に失敗しました。');
    return [];
  }
}

function showError(message) {
  const el = document.getElementById('error-area');
  if (el) {
    el.textContent = message;
    el.style.display = 'block';
  }
}
```

---

## よく使うコンポーネントパターン

### ローディングスピナー

```html
<div id="loading" class="mc-loading">
  <div class="mc-spinner"></div>
  <p>読み込み中...</p>
</div>
```

```css
.mc-loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: var(--mc-space-xl);
  color: var(--mc-text-sub);
}
.mc-spinner {
  width: 32px; height: 32px;
  border: 3px solid var(--mc-border);
  border-top-color: var(--mc-primary);
  border-radius: 50%;
  animation: mc-spin 0.8s linear infinite;
}
@keyframes mc-spin { to { transform: rotate(360deg); } }
```

### データテーブル

```html
<div class="mc-table-wrap">
  <table class="mc-table">
    <thead>
      <tr>
        <th>項目1</th>
        <th>項目2</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody id="table-body">
      <!-- JS で行を追加 -->
    </tbody>
  </table>
</div>
```

```css
.mc-table-wrap { overflow-x: auto; }
.mc-table {
  width: 100%;
  border-collapse: collapse;
  font-size: var(--mc-font-size-sm, 14px);
}
.mc-table th, .mc-table td {
  padding: 10px 12px;
  text-align: left;
  border-bottom: 1px solid var(--mc-border);
}
.mc-table th {
  background: var(--mc-bg-gray);
  font-weight: 600;
  white-space: nowrap;
}
.mc-table tr:hover td { background: #faf9f8; }
```

### ボタン

```html
<button class="mc-btn mc-btn--primary" onclick="handleClick()">送信</button>
<button class="mc-btn mc-btn--secondary" onclick="handleCancel()">キャンセル</button>
```

```css
.mc-btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  border: 1px solid transparent;
  border-radius: 4px;
  font-family: var(--mc-font);
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s, border-color 0.15s;
}
.mc-btn--primary {
  background: var(--mc-primary);
  color: #fff;
}
.mc-btn--primary:hover { background: var(--mc-primary-dark); }
.mc-btn--secondary {
  background: #fff;
  color: var(--mc-primary);
  border-color: var(--mc-primary);
}
.mc-btn--secondary:hover { background: var(--mc-primary-light); }
```

---

## ファイルのアップロードと更新手順

1. HTML ファイルを作成（ローカルで動作確認）
2. SharePoint のドキュメントライブラリにアップロード
3. sp-html-viewer Web パーツのプロパティペインでファイルを選択
4. 更新時はファイルを上書きアップロードするだけで即時反映

> **注意**: HTML ファイルを別ライブラリに移動すると Web パーツのリンクが切れる。ライブラリは運用開始前に確定させる。

---

## デバッグ方法

```javascript
// 開発中は console.log を活用（SharePoint の開発者ツールで確認）
console.log('[sp-html-viewer] データ取得完了:', data);

// 本番前に削除またはフラグで制御
const DEBUG = false;
function log(...args) { if (DEBUG) console.log('[mc]', ...args); }
```

---

## チェックリスト（HTML 作成完了時）

- [ ] DOCTYPE・charset・viewport が正しく設定されている
- [ ] クラス名にプレフィックスが付いている
- [ ] CSS カスタムプロパティでデザイントークンを管理している
- [ ] `height: 100vh` を埋め込みモードで使っていない
- [ ] JS は `DOMContentLoaded` でラップしている
- [ ] エラーハンドリングが実装されている
- [ ] ローカルブラウザで動作確認済み
- [ ] SharePoint REST API のサイトパスが正しい
