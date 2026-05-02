---
updated: 2026-05-02
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
