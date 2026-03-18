---
name: a11y-check
description: アクセシビリティ（WCAG 2.1）の確認・修正提案を行うスキル。"アクセシビリティ", "a11y", "スクリーンリーダー", "キーボード操作", "WCAG", "色コントラスト", "aria" などのキーワードで使用する。既存コードのアクセシビリティ問題を検出し、具体的な修正コードを提案する。
---

# アクセシビリティチェック

WCAG 2.1 AA 準拠を目標に、HTML/CSS/JavaScript のアクセシビリティを検証・改善します。

## チェック項目

### 1. 色コントラスト（WCAG 1.4.3）
- 通常テキスト: コントラスト比 **4.5:1 以上**
- 大きいテキスト（18px以上 or 14px太字）: **3:1 以上**
- UI コンポーネント・グラフィック: **3:1 以上**

```css
/* NG 例 */
color: #767676; background: #ffffff; /* 比率 4.48:1 — ギリギリNG */

/* OK 例 */
color: #595959; background: #ffffff; /* 比率 7:1 — AA/AAA 合格 */
```

### 2. キーボード操作（WCAG 2.1.1）
- 全インタラクティブ要素が Tab でフォーカス可能か
- フォーカスインジケーターが視覚的に確認できるか
- カスタムコンポーネントに適切なキーボードハンドラがあるか

```css
/* フォーカスリング — outline を消さない */
:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

### 3. ARIA・セマンティック HTML（WCAG 4.1.2）
```html
<!-- NG: div クリック -->
<div onclick="open()">メニュー</div>

<!-- OK: button + aria -->
<button aria-expanded="false" aria-controls="menu-list">メニュー</button>
<ul id="menu-list" role="menu" hidden>...</ul>
```

### 4. 画像代替テキスト（WCAG 1.1.1）
```html
<!-- 意味のある画像 -->
<img src="chart.png" alt="2024年度売上グラフ：Q4が最高値">

<!-- 装飾画像 -->
<img src="divider.png" alt="">
```

### 5. フォームラベル（WCAG 1.3.1）
```html
<!-- NG -->
<input type="text" placeholder="名前">

<!-- OK -->
<label for="name">名前 <span aria-hidden="true">*</span></label>
<input id="name" type="text" required aria-required="true">
```

### 6. ランドマーク（WCAG 1.3.6）
```html
<header role="banner">...</header>
<nav aria-label="メインナビゲーション">...</nav>
<main>...</main>
<aside aria-label="関連リンク">...</aside>
<footer role="contentinfo">...</footer>
```

## SharePoint 向け注意点

- SharePoint のマスターページに既存ランドマークがあるため重複しないよう確認
- ScriptLoader で挿入する要素は `<main>` 相当の領域内に配置する
- SPFx Web パーツは `role="region"` と `aria-label` を必ず付与する

## レポート形式

問題を発見した場合は以下の形式で報告する:

```
【重要度: 高/中/低】
場所: <ファイル名:行番号>
問題: <WCAG 基準番号> — <問題の説明>
修正案:
  <修正後コード>
```
