---
name: css-layout
description: レスポンシブ CSS レイアウトを設計・生成するスキル。"レイアウト", "レスポンシブ", "グリッド", "フレックスボックス", "カラム", "サイドバー", "ヘッダー・フッター", "カード並び", "モバイル対応" などのキーワードが出たら積極的に使用する。CSS Grid / Flexbox を使った現代的なレイアウト実装を提供し、ブレークポイント設計からダークモード対応までカバーする。
---

# CSS レイアウト設計

CSS Grid / Flexbox を活用したレスポンシブレイアウトを生成します。

## 設計原則

1. **モバイルファースト**: `min-width` ブレークポイントで設計
2. **CSS カスタムプロパティ**: スペーシング・カラーを変数化
3. **Grid と Flex の使い分け**: 2次元 → Grid、1次元 → Flex
4. **コンテナクエリ**: 可能な限りコンテナクエリを活用

## ブレークポイント標準

```css
:root {
  --bp-sm: 640px;   /* スマートフォン (横) */
  --bp-md: 768px;   /* タブレット */
  --bp-lg: 1024px;  /* デスクトップ */
  --bp-xl: 1280px;  /* ワイドスクリーン */
}

/* 使用例 */
@media (min-width: 768px) { ... }
@media (min-width: 1024px) { ... }
```

## レイアウトパターン

### 聖杯レイアウト (Header / Sidebar / Main / Footer)
```css
.layout {
  display: grid;
  grid-template:
    "header header header" auto
    "sidebar main aside" 1fr
    "footer footer footer" auto
    / 240px 1fr 200px;
  min-height: 100vh;
  gap: var(--space-md);
}

.layout__header { grid-area: header; }
.layout__sidebar { grid-area: sidebar; }
.layout__main   { grid-area: main; }
.layout__aside  { grid-area: aside; }
.layout__footer { grid-area: footer; }

/* モバイル */
@media (max-width: 767px) {
  .layout {
    grid-template:
      "header" auto
      "main" 1fr
      "sidebar" auto
      "footer" auto
      / 1fr;
  }
  .layout__aside { display: none; }
}
```

### カードグリッド (Auto-fill)
```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: var(--space-lg);
  padding: var(--space-lg);
}
```

### スティッキーヘッダー + スクロールコンテンツ
```css
.page {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
.page__header {
  position: sticky;
  top: 0;
  z-index: 100;
  flex-shrink: 0;
}
.page__content {
  flex: 1;
  overflow-y: auto;
}
```

## CSS カスタムプロパティ標準セット

```css
:root {
  /* スペーシング */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 40px;

  /* カラー */
  --color-primary: #0078d4;
  --color-secondary: #605e5c;
  --color-bg: #ffffff;
  --color-surface: #f3f2f1;
  --color-text: #323130;
  --color-border: #edebe9;

  /* タイポグラフィ */
  --font-size-sm: 12px;
  --font-size-md: 14px;
  --font-size-lg: 16px;
  --font-size-xl: 20px;
  --font-size-2xl: 28px;

  /* シャドウ */
  --shadow-sm: 0 1px 3px rgba(0,0,0,.1);
  --shadow-md: 0 4px 12px rgba(0,0,0,.12);
}

/* ダークモード */
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #1b1a19;
    --color-surface: #2b2a29;
    --color-text: #f3f2f1;
    --color-border: #3b3a39;
  }
}
```

## 作業フロー

1. レイアウトの目的・ページ構造を確認する
2. ワイヤーフレームや参考デザインがあれば確認する
3. Grid / Flex どちらが適切か判断する
4. モバイルファーストで CSS を記述する
5. ブレークポイントごとに動作確認ポイントを提示する

## 更新履歴
- v1.0: 初版作成
