---
name: design-system
description: デザインシステムの構築・標準化を行うスキル。"デザインシステム", "カラーパレット", "タイポグラフィ", "コンポーネント標準化", "トークン", "スタイルガイド" などのキーワードで使用する。CSS カスタムプロパティを中心に、一貫性のある UI 基盤を設計・文書化する。
---

# デザインシステム構築

プロジェクト全体で一貫した UI を実現するためのデザイン基盤を設計します。

## デザイントークン標準

### カラー
```css
:root {
  /* ブランドカラー */
  --color-primary:       #0078d4;  /* Microsoft Blue */
  --color-primary-dark:  #005a9e;
  --color-primary-light: #c7e0f4;

  /* セマンティックカラー */
  --color-success: #107c10;
  --color-warning: #ff8c00;
  --color-error:   #d13438;
  --color-info:    #0078d4;

  /* ニュートラル */
  --color-text-primary:   #201f1e;
  --color-text-secondary: #605e5c;
  --color-text-disabled:  #a19f9d;
  --color-bg:             #ffffff;
  --color-bg-secondary:   #f3f2f1;
  --color-border:         #edebe9;
}
```

### タイポグラフィ
```css
:root {
  --font-family: "Segoe UI", "Yu Gothic UI", sans-serif;

  --font-size-xs:   12px;
  --font-size-sm:   14px;
  --font-size-md:   16px;
  --font-size-lg:   18px;
  --font-size-xl:   20px;
  --font-size-2xl:  24px;
  --font-size-3xl:  32px;

  --font-weight-regular: 400;
  --font-weight-medium:  500;
  --font-weight-bold:    700;

  --line-height-tight:  1.2;
  --line-height-normal: 1.5;
  --line-height-loose:  1.8;
}
```

### スペーシング
```css
:root {
  --space-xs:  4px;
  --space-sm:  8px;
  --space-md:  16px;
  --space-lg:  24px;
  --space-xl:  32px;
  --space-2xl: 48px;
  --space-3xl: 64px;
}
```

### シャドウ・角丸
```css
:root {
  --radius-sm: 2px;
  --radius-md: 4px;
  --radius-lg: 8px;
  --radius-xl: 12px;
  --radius-full: 9999px;

  --shadow-sm: 0 1px 3px rgba(0,0,0,.12);
  --shadow-md: 0 4px 8px rgba(0,0,0,.14);
  --shadow-lg: 0 8px 16px rgba(0,0,0,.14);
}
```

## SharePoint 向け考慮事項

- SharePoint の既存スタイルとの競合を避けるため、クラス名はプロジェクト固有プレフixを付ける（例: `.mc-button`, `.mc-card`）
- `global.css` にトークンを定義し、各 `SitePages__*.css` で参照する
- Fluent UI のデザイン原則（Microsoft のデザインシステム）を参考に設計する

## 文書化テンプレート

各コンポーネントに以下を記録する:
- 用途・使用場面
- HTML 構造
- CSS クラス一覧
- バリエーション（サイズ・状態・カラー）
- アクセシビリティ要件
