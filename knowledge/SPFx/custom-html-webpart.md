---
updated: 2026-05-09
tags:
  - spfx
  - sharepoint
  - webpart
---

# カスタム HTML Webパーツ 実装知見

## 概要

HTML/CSS/JS を textarea に入力して iframe でレンダリングする SPFx Webパーツ。
実装: `MyCorp/SPFx/custom-html-webpart/`

## iframe 描画方式

### sandbox 問題

- `sandbox` 属性を付けると親から `contentDocument` にアクセス不可（別オリジン扱い）
- `sandbox` + `postMessage` も SharePoint Online workbench ではブロックされる
- **解決策**: `sandbox` を外し、`allowScripts=OFF` 時は script タグを除去して安全性を確保

### doc.write によるコンテンツ書き込み

`sandbox` なしなら `doc.write()` で `contentDocument` に直接書き込み可能。
完全な HTML 文書（DOCTYPE+head+body）で包まないと `scrollHeight` が正しく計算されない。

```typescript
const fullDoc = `<!DOCTYPE html>
<html><head>
<style>html, body { margin: 0; padding: 0; overflow: hidden; box-sizing: border-box; }</style>
</head><body>${safeHtml}</body></html>`;

const doc = iframe.contentDocument ?? iframe.contentWindow?.document;
doc.open(); doc.write(fullDoc); doc.close();
```

## 自動高さ調整パターン

```typescript
iframe.onload = () => {
  this._adjustHeight(iframe, minH);
  setTimeout(() => this._adjustHeight(iframe, minH), 300); // 遅延読み込み対応
};

private _adjustHeight(iframe: HTMLIFrameElement, minH: number): void {
  const body = iframe.contentDocument?.body;
  const html = iframe.contentDocument?.documentElement;
  if (!body || !html) return;

  iframe.style.height = '1px'; // 一旦縮める（高さ減少にも追従するため）

  const measured = Math.max(
    body.scrollHeight, body.offsetHeight,
    html.scrollHeight, html.offsetHeight
  );
  iframe.style.height = `${(measured > 0 ? measured : minH) + 4}px`;
}
```

### ポイント

- iframe の **CSS の min-height を削除**する（JS の高さ設定が上書きされるため）
- リセットは `minH` ではなく `1px` にする（小さいコンテンツにも追従）
- `minH` はフォールバック（measured=0 のとき）のみ使用

## セキュリティ

| 設定 | 動作 |
|------|------|
| `allowScripts=OFF`（デフォルト） | script タグ・インラインイベントを除去 |
| `allowScripts=ON` | 管理者のみ推奨、JS がそのまま実行される |

## アイコン設定（base64）

`manifest.json` の `officeFabricIconFontName` を `iconImageUrl` に変更：

```json
"iconImageUrl": "data:image/png;base64,<base64文字列>"
```

PNG ファイルを base64 に変換するコマンド：

```bash
python3 -c "
import base64
with open('icon.png', 'rb') as f:
    print('data:image/png;base64,' + base64.b64encode(f.read()).decode())
"
```

## 空コンテンツ時の非表示

閲覧モードで HTML が空のとき CanvasControl ごと非表示にする：

```typescript
if (!isEdit && !savedHtml) {
  this.domElement.innerHTML = '';
  const canvasControl = this.domElement.closest<HTMLElement>('[data-automation-id="CanvasControl"]');
  if (canvasControl) canvasControl.style.display = 'none';
  return;
}
```

---

## SharePoint CSP 制約と HTML/JS 記述ルール

> **現行実装（2026-05-09 以降）**: iframe ではなく `#preview` div への直接 DOM 注入方式。

### SharePoint の CSP 方針

SharePoint Online のモダンページは **nonce ベースの CSP** (`'strict-dynamic'`) を使用している。

```
Content-Security-Policy: script-src 'nonce-XXXX' 'strict-dynamic' ...
```

### 動作する記述 vs ブロックされる記述

| 記述方法 | 動作 | 理由 |
|---------|------|------|
| `<script>` タグ内の `addEventListener` | **動作する** | nonce 付き createElement で注入するため CSP パス |
| `onclick="..."` などのインラインイベント属性 | **ブロックされる** | `'strict-dynamic'` は HTML 属性ハンドラを許可しない |
| `href="javascript:..."` | **ブロックされる** | javascript: URL スキームは CSP でブロック |
| `<script src="外部URL">` | 条件付き許可 | ドメインが CSP の allowlist にある場合のみ |

### イベントハンドラの正しい書き方

```html
<!-- NG: CSP でブロックされる -->
<button onclick="showSection('colors')">カラーパレット</button>

<!-- OK: script タグ内で addEventListener を使う -->
<button id="btn-colors">カラーパレット</button>
<script>
  document.getElementById('btn-colors').addEventListener('click', function() {
    showSection('colors');
  });
</script>
```

複数要素への一括設定:

```html
<button class="tab-btn" data-section="colors">カラーパレット</button>
<button class="tab-btn" data-section="typography">タイポグラフィ</button>

<script>
  document.querySelectorAll('.tab-btn').forEach(function(btn) {
    btn.addEventListener('click', function() {
      showSection(this.dataset.section);
    });
  });
</script>
```

### Nonce の取得方法

モダンブラウザは `getAttribute('nonce')` をセキュリティ仕様で空文字にする。以下の順で試みる:

```typescript
private _getNonce(): string {
  // 1. webpack グローバル（SPFx 環境で最も安定）
  const wpNonce = (window as any).__webpack_nonce__;
  if (typeof wpNonce === 'string' && wpNonce) return wpNonce;

  // 2. meta タグ経由
  const metaNonce = document.querySelector<HTMLMetaElement>('meta[name="csp-nonce"]');
  if (metaNonce?.content) return metaNonce.content;

  // 3. IDL プロパティ経由（一部環境）
  const scripts = document.querySelectorAll<HTMLScriptElement>('script[nonce]');
  for (let i = 0; i < scripts.length; i++) {
    const n = scripts[i].nonce || scripts[i].getAttribute('nonce') || '';
    if (n) return n;
  }
  return '';
}
```

### Script 注入パターン（動作確認済み）

```typescript
// HTML 内の <script> タグを nonce 付きで動的注入
const scriptEl = document.createElement('script');
if (nonce) scriptEl.setAttribute('nonce', nonce);
scriptEl.text = jsCode;
preview.appendChild(scriptEl); // innerHTML への代入では実行されない
```

> **重要**: `innerHTML` に script タグを含めても実行されない。必ず `createElement` + `appendChild` で注入する。

### TypeScript 注意点

| 問題 | 解決策 |
|------|--------|
| `Array.from` が使えない (ES5 target) | `for (let i = 0; i < nodeList.length; i++)` を使う |
| `querySelectorAll` の結果を `.forEach` | ES5 では NodeList に forEach がない場合があるため for ループ推奨 |

### プロパティ構成（現行）

| プロパティ | 型 | 用途 |
|-----------|-----|------|
| `customHtml` | string | 編集エリアの textarea に表示・保存される HTML/CSS |
| `allowScripts` | boolean | ON のとき script を nonce 付き注入で実行 |
| `customJavaScript` | string | プロパティパネルから入力する JS（レンダリング時に実行） |
