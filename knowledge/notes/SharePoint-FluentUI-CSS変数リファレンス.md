---
date: 2026-04-30
tags:
  - agent/sharepoint
  - agent/designer
  - agent/frontend
  - type/📚reference
  - project/sharepoint
---

# SharePoint 内部 UIカラー変数（Fluent UI）リファレンス

## 内容

SharePoint のページ内に `.fui-FluentProvider1` クラスで注入される Fluent UI デザイントークン（CSS カスタムプロパティ）の完全リファレンス。
SPFx Web パーツや sp-html-viewer でカスタム UI を組む際、これらの変数を `var(--変数名)` の形式で参照することで SharePoint テーマとのトーン統一が可能。

> ⚠️ このトークンセットは **SharePoint Online のライトテーマ（Teal 系）** のスナップショット。テナントテーマを変更した場合は値が変わる可能性がある。

---

## ブランドカラー（Teal 系）

SharePoint のアクセントカラー。ボタン・リンク・フォーカスリングなどに使用。

| 変数名 | 値 | 用途 |
|---|---|---|
| `--colorBrandBackground` | `#038387` | ブランド背景（プライマリボタン背景） |
| `--colorBrandBackgroundHover` | `#02767a` | ホバー時 |
| `--colorBrandBackgroundPressed` | `#02494c` | クリック時 |
| `--colorBrandBackgroundSelected` | `#026367` | 選択時 |
| `--colorBrandForeground1` | `#038387` | ブランドテキスト |
| `--colorBrandForeground2` | `#02767a` | サブブランドテキスト |
| `--colorBrandForegroundLink` | `#02767a` | リンクテキスト |
| `--colorBrandForegroundLinkHover` | `#026367` | リンクホバー |
| `--colorBrandStroke1` | `#038387` | ブランドボーダー |
| `--colorBrandStroke2` | `#9bd9db` | 薄めブランドボーダー |
| `--colorBrandBackground2` | `#f0fafa` | 淡いブランド背景（バッジ・タグ等） |
| `--vivaConnectionsSoftBackground` | `#f0fafa` | Viva Connections 薄背景 |

---

## ニュートラル前景色（テキスト）

| 変数名 | 値 | 用途 |
|---|---|---|
| `--colorNeutralForeground1` | `#37474f` | メインテキスト |
| `--colorNeutralForeground2` | `#7b8c95` | サブテキスト |
| `--colorNeutralForeground3` | `#818a8f` | プレースホルダー等 |
| `--colorNeutralForegroundDisabled` | `#c8c8c8` | 無効テキスト |
| `--colorNeutralForegroundInverted` | `#ffffff` | 反転テキスト（ダーク背景上） |
| `--colorNeutralForegroundOnBrand` | `#ffffff` | ブランド背景上のテキスト |

---

## ニュートラル背景色

| 変数名 | 値 | 用途 |
|---|---|---|
| `--colorNeutralBackground1` | `#ffffff` | ページ/カード基本背景 |
| `--colorNeutralBackground2` | `#f8f8f8` | セクション背景 |
| `--colorNeutralBackground3` | `#f4f4f4` | 内側ネスト背景 |
| `--colorNeutralBackground4` | `#f4f4f4` | さらに内側 |
| `--colorNeutralBackground5` | `#eaeaea` | ストライプ等 |
| `--colorNeutralCardBackground` | `#fafafa` | カード背景 |
| `--colorNeutralBackgroundDisabled` | `#f4f4f4` | 無効状態背景 |
| `--colorBackgroundOverlay` | `rgba(0,0,0,0.4)` | モーダルオーバーレイ |

---

## ストローク（ボーダー）

| 変数名 | 値 | 用途 |
|---|---|---|
| `--colorNeutralStroke1` | `#d0d0d0` | 標準ボーダー |
| `--colorNeutralStroke2` | `#dadada` | 薄めボーダー |
| `--colorNeutralStroke3` | `#f4f4f4` | 極薄ボーダー |
| `--colorNeutralStrokeAccessible` | `#7b8c95` | アクセシブルボーダー（WCAG 対応） |
| `--colorNeutralStrokeAccessibleSelected` | `#038387` | 選択時アクセシブルボーダー |
| `--colorNeutralStrokeDisabled` | `#dadada` | 無効状態ボーダー |
| `--colorStrokeFocus1` | `#ffffff` | フォーカスリング（内側） |
| `--colorStrokeFocus2` | `#1f282c` | フォーカスリング（外側） |

---

## セマンティックトークン（ボタン・フォーム）

SharePoint が独自定義するセマンティック変数。SPFx / sp-html-viewer で積極的に活用する。

| 変数名 | 値 | 用途 |
|---|---|---|
| `--semanticPrimaryButtonBackground` | `#038387` | プライマリボタン背景 |
| `--semanticPrimaryButtonBackgroundHovered` | `#02767a` | ホバー |
| `--semanticPrimaryButtonBackgroundPressed` | `#026367` | プレス |
| `--semanticPrimaryButtonText` | `#ffffff` | ボタンテキスト |
| `--semanticPrimaryButtonTextDisabled` | `#d0d0d0` | 無効テキスト |
| `--semanticButtonBackground` | `#ffffff` | セカンダリボタン背景 |
| `--semanticButtonBorder` | `#8a8886` | セカンダリボタンボーダー |
| `--semanticButtonBackgroundHovered` | `#f4f4f4` | ホバー |
| `--semanticButtonBackgroundPressed` | `#eaeaea` | プレス |
| `--semanticButtonTextHovered` | `#2a363c` | ホバーテキスト |
| `--semanticButtonBackgroundDisabled` | `#f4f4f4` | 無効背景 |
| `--semanticButtonTextDisabled` | `#b8c4ca` | 無効テキスト |
| `--semanticBodyText` | `#37474f` | 本文テキスト |
| `--semanticInputBackground` | `#ffffff` | 入力フィールド背景 |
| `--semanticInputText` | `#37474f` | 入力テキスト |
| `--semanticErrorText` | `#a4262c` | エラーメッセージ |
| `--semanticActionLink` | `#37474f` | アクションリンク |
| `--semanticDisabledText` | `#b8c4ca` | 無効テキスト |
| `--semanticDisabledBackground` | `#f4f4f4` | 無効背景 |

---

## ステータスカラー

| 変数名 | 値 | 用途 |
|---|---|---|
| `--colorStatusSuccessBackground1` | `#f1faf1` | 成功・薄背景 |
| `--colorStatusSuccessBackground3` | `#107c10` | 成功・強背景 |
| `--colorStatusSuccessForeground1` | `#0e700e` | 成功テキスト |
| `--colorStatusSuccessBorderActive` | `#107c10` | 成功ボーダー |
| `--colorStatusWarningBackground1` | `#fff9f5` | 警告・薄背景 |
| `--colorStatusWarningBackground3` | `#f7630c` | 警告・強背景 |
| `--colorStatusWarningForeground1` | `#bc4b09` | 警告テキスト |
| `--colorStatusWarningBorderActive` | `#f7630c` | 警告ボーダー |
| `--colorStatusDangerBackground1` | `#fdf3f4` | エラー・薄背景 |
| `--colorStatusDangerBackground3` | `#c50f1f` | エラー・強背景 |
| `--colorStatusDangerForeground1` | `#b10e1c` | エラーテキスト |
| `--colorStatusDangerBorderActive` | `#c50f1f` | エラーボーダー |

---

## タイポグラフィ

| 変数名 | 値 |
|---|---|
| `--fontFamilyBase` | `'Segoe UI', -apple-system, BlinkMacSystemFont, Roboto, 'Helvetica Neue', sans-serif` |
| `--fontFamilyMonospace` | `Consolas, 'Courier New', Courier, monospace` |
| `--fontFamilyNumeric` | `Bahnschrift, 'Segoe UI', sans-serif` |
| `--fontSizeBase100` | `10px` |
| `--fontSizeBase200` | `12px` |
| `--fontSizeBase300` | `14px` ← **本文標準** |
| `--fontSizeBase400` | `16px` |
| `--fontSizeBase500` | `20px` |
| `--fontSizeBase600` | `24px` |
| `--fontSizeHero700` | `28px` |
| `--fontSizeHero800` | `32px` |
| `--fontSizeHero900` | `40px` |
| `--fontSizeHero1000` | `68px` |
| `--fontWeightRegular` | `400` |
| `--fontWeightMedium` | `500` |
| `--fontWeightSemibold` | `600` |
| `--fontWeightBold` | `700` |

---

## スペーシング

### 水平

| 変数名 | 値 |
|---|---|
| `--spacingHorizontalXXS` | `2px` |
| `--spacingHorizontalXS` | `4px` |
| `--spacingHorizontalS` | `8px` |
| `--spacingHorizontalM` | `12px` |
| `--spacingHorizontalL` | `16px` |
| `--spacingHorizontalXL` | `20px` |
| `--spacingHorizontalXXL` | `24px` |
| `--spacingHorizontalXXXL` | `32px` |

### 垂直

| 変数名 | 値 |
|---|---|
| `--spacingVerticalXXS` | `2px` |
| `--spacingVerticalXS` | `4px` |
| `--spacingVerticalS` | `8px` |
| `--spacingVerticalM` | `12px` |
| `--spacingVerticalL` | `16px` |
| `--spacingVerticalXL` | `20px` |
| `--spacingVerticalXXL` | `24px` |
| `--spacingVerticalXXXL` | `32px` |

---

## 角丸

| 変数名 | 値 |
|---|---|
| `--borderRadiusNone` | `0` |
| `--borderRadiusSmall` | `2px` |
| `--borderRadiusMedium` | `4px` |
| `--borderRadiusLarge` | `6px` |
| `--borderRadiusXLarge` | `8px` |
| `--borderRadius2XLarge` | `12px` |
| `--borderRadius3XLarge` | `16px` |
| `--borderRadius4XLarge` | `24px` |
| `--borderRadius5XLarge` | `32px` |
| `--borderRadius6XLarge` | `40px` |
| `--borderRadiusCircular` | `10000px` |

---

## シャドウ

| 変数名 | 値 |
|---|---|
| `--shadow2` | `0 0 2px rgba(0,0,0,.12), 0 1px 2px rgba(0,0,0,.14)` |
| `--shadow4` | `0 0 2px rgba(0,0,0,.12), 0 2px 4px rgba(0,0,0,.14)` |
| `--shadow8` | `0 0 2px rgba(0,0,0,.12), 0 4px 8px rgba(0,0,0,.14)` |
| `--shadow16` | `0 0 2px rgba(0,0,0,.12), 0 8px 16px rgba(0,0,0,.14)` |
| `--shadow28` | `0 0 8px rgba(0,0,0,.12), 0 14px 28px rgba(0,0,0,.14)` |
| `--shadow64` | `0 0 8px rgba(0,0,0,.12), 0 32px 64px rgba(0,0,0,.14)` |

---

## アニメーション

### Duration

| 変数名 | 値 |
|---|---|
| `--durationUltraFast` | `50ms` |
| `--durationFaster` | `100ms` |
| `--durationFast` | `150ms` |
| `--durationNormal` | `200ms` |
| `--durationGentle` | `250ms` |
| `--durationSlow` | `300ms` |
| `--durationSlower` | `400ms` |
| `--durationUltraSlow` | `500ms` |

### Easing

| 変数名 | 値 |
|---|---|
| `--curveDecelerateMax` | `cubic-bezier(0.1, 0.9, 0.2, 1)` |
| `--curveDecelerateMid` | `cubic-bezier(0, 0, 0, 1)` |
| `--curveEasyEase` | `cubic-bezier(0.33, 0, 0.67, 1)` |
| `--curveEasyEaseMax` | `cubic-bezier(0.8, 0, 0.2, 1)` |
| `--curveLinear` | `cubic-bezier(0, 0, 1, 1)` |
| `--curveAccelerateMax` | `cubic-bezier(0.9, 0.1, 1, 0.2)` |

---

## 使い方（SPFx / sp-html-viewer）

```css
/* SPFx Web パーツや sp-html-viewer の CSS で直接参照可能 */

.my-button {
  background-color: var(--semanticPrimaryButtonBackground);
  color: var(--semanticPrimaryButtonText);
  border-radius: var(--borderRadiusMedium);
  font-family: var(--fontFamilyBase);
  font-size: var(--fontSizeBase300);
  font-weight: var(--fontWeightSemibold);
  padding: var(--spacingVerticalS) var(--spacingHorizontalL);
  transition: background-color var(--durationFast) var(--curveEasyEase);
}

.my-button:hover {
  background-color: var(--semanticPrimaryButtonBackgroundHovered);
}

.my-card {
  background: var(--colorNeutralBackground1);
  border: 1px solid var(--colorNeutralStroke1);
  border-radius: var(--borderRadiusLarge);
  box-shadow: var(--shadow4);
  padding: var(--spacingVerticalL) var(--spacingHorizontalL);
}

.my-error-message {
  color: var(--semanticErrorText);
  font-size: var(--fontSizeBase200);
}
```

---

## カラーパレット（アクセント色）

ステータス・バッジ・タグ等に使用する拡張パレット。変数名パターン:
`--colorPalette{色名}{Background|Foreground|Border}{1|2|3|Active}`

主要カラー名: `Red` / `Green` / `DarkOrange` / `Yellow` / `Berry` / `LightGreen` / `Marigold` / `Teal` / `Blue` / `RoyalBlue` / `Cornflower` / `Navy` / `Lavender` / `Purple` / `Grape` / `Lilac` / `Pink` / `Magenta` / `Plum` / `Beige` / `Mink` / `Platinum` / `Anchor` / `Seafoam` / `Forest` / `DarkGreen` / `LightTeal` / `Steel` / `DarkRed` / `Cranberry` / `Pumpkin` / `Peach` / `Gold` / `Brass` / `Brown`

---

## 元ファイル

- ソース: SharePoint ページ DOM より `.fui-FluentProvider1` クラスの CSS を抽出
- 取得日: 2026-04-30
- ファイル名: `fui-FluentProvider.txt`

---

## 関連

- [[agents/05_sharepoint/CLAUDE]]
- [[agents/06_frontend/CLAUDE]]
- [[agents/04_designer/CLAUDE]]
- [[2026-04-30_SharePoint-sp-html-viewer]]
- [[agents/05_sharepoint/skills/xsp-script-loader]]
- [[agents/05_sharepoint/skills/spfx-component]]
