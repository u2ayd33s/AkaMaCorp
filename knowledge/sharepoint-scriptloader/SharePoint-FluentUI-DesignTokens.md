---
date: 2026-04-30
tags:
  - agent/sharepoint
  - agent/designer
  - agent/frontend
  - type/📚reference
  - type/💡insight
  - project/sharepoint
---

# SharePoint Fluent UI（v9）デザイントークン リファレンス

SharePoint / Microsoft 365 全体で採用されている **Fluent UI v9（Fluent 2）** のデザイントークン体系。
`FluentProvider` が CSS カスタムプロパティ（CSS 変数）として全トークンを公開しており、SharePoint カスタマイズ・SPFx・sp-html-viewer・xsp-script-loader での装飾を **SharePoint UI と整合させる際に参照する**。

> 📌 提供された CSS は `.fui-FluentProvider3` クラスに展開されたトークンのスナップショット。SharePoint Online（Teal ブランドカラー `#038387`）のライトテーマの値。

---

## 1. 重要な背景：SharePoint と Fluent UI のバージョン事情

| 項目 | 状況 |
|------|------|
| **SharePoint UI 本体** | Fluent UI v9（Fluent 2）でレンダリングされている |
| **SPFx（SharePoint Framework）** | 公式は **Fluent UI v8 まで**。React 17 固定のため v9 非対応 |
| **v9 を使うには** | React 18 を自前で同梱する必要あり（非推奨） |
| **回避策** | CSS 変数（このファイルのトークン）を直接参照して見た目だけ揃える |

→ **SPFx 開発では v8 を使い、CSS で v9 のトークンを参照するハイブリッド戦略が現実的。**

参考: [The Fluent UI 9 React Problem in SharePoint Framework - N8D](https://n8d.at/the-fluent-ui-react-problem-in-sharepoint-framework/)

---

## 2. トークンカテゴリ一覧

Fluent UI v9 のトークンは「セマンティック（意味）」で命名される。色は `#xxx` ではなく **役割** で参照する。

| カテゴリ | プレフィックス | 用途 |
|---------|--------------|------|
| 角丸 | `--borderRadius*` | None / Small(2px) / Medium(4px) / Large(6px) / XLarge(8px) / Circular(10000px) |
| フォントサイズ | `--fontSize*` | Base100(10px) 〜 Hero1000(68px) |
| 行高 | `--lineHeight*` | Base100(14px) 〜 Hero1000(92px) |
| フォント | `--fontFamily*` | Base / Monospace / Numeric |
| ウェイト | `--fontWeight*` | Regular(400) / Medium(500) / Semibold(600) / Bold(700) |
| ストローク幅 | `--strokeWidth*` | Thin(1px) / Thick(2px) / Thicker(3px) / Thickest(4px) |
| 横スペース | `--spacingHorizontal*` | None 〜 XXXL(32px) |
| 縦スペース | `--spacingVertical*` | None 〜 XXXL(32px) |
| アニメ時間 | `--duration*` | UltraFast(50ms) 〜 UltraSlow(500ms) |
| イージング | `--curve*` | Accelerate / Decelerate / EasyEase |
| 文字色 | `--colorNeutralForeground*` | 1〜5、Disabled、Inverted、OnBrand |
| 背景色 | `--colorNeutralBackground*` | 1〜8、Inverted、Static、Alpha |
| 線色 | `--colorNeutralStroke*` | 1〜4、Accessible、OnBrand |
| ブランド色 | `--colorBrand*`, `--colorCompoundBrand*` | プライマリアクション |
| 影 | `--shadow*` | 2 / 4 / 8 / 16 / 28 / 64（数値=Y方向の伸び量） |
| ステータス | `--colorStatus*` | Success / Warning / Danger |
| パレット | `--colorPalette*` | Red / Green / Blue / Teal など16色×3階調 |

---

## 3. よく使う主要トークン（実用版チートシート）

### 3.1 テキストカラー

```css
.body-text       { color: var(--colorNeutralForeground1); }   /* #37474f メインテキスト */
.secondary-text  { color: var(--colorNeutralForeground2); }   /* #7b8c95 サブテキスト */
.muted-text      { color: var(--colorNeutralForeground3); }   /* #818a8f キャプション */
.disabled-text   { color: var(--colorNeutralForegroundDisabled); } /* #c8c8c8 */
.link            { color: var(--colorBrandForegroundLink); }  /* #02767a リンク */
.link:hover      { color: var(--colorBrandForegroundLinkHover); }
.on-brand-text   { color: var(--colorNeutralForegroundOnBrand); } /* #ffffff ボタン上 */
```

### 3.2 背景色

```css
.surface-1   { background: var(--colorNeutralBackground1); }  /* #ffffff カード等の標準面 */
.surface-2   { background: var(--colorNeutralBackground2); }  /* #f8f8f8 サブ領域 */
.surface-3   { background: var(--colorNeutralBackground3); }  /* #f4f4f4 区切り背景 */
.subtle-hover { background: var(--colorSubtleBackgroundHover); } /* #f4f4f4 ホバー */
.brand-bg    { background: var(--colorBrandBackground); }     /* #038387 プライマリボタン */
.brand-bg:hover { background: var(--colorBrandBackgroundHover); } /* #02767a */
.brand-bg-soft { background: var(--colorBrandBackground2); }  /* #f0fafa Viva Connections soft */
```

### 3.3 ボーダー（線）

```css
.border          { border: var(--strokeWidthThin) solid var(--colorNeutralStroke1); }   /* #d0d0d0 */
.border-subtle   { border: 1px solid var(--colorNeutralStrokeSubtle); }                 /* #dadada */
.border-accessible { border: 1px solid var(--colorNeutralStrokeAccessible); }           /* #7b8c95 アクセシビリティ準拠 */
.focus-ring      { outline: 2px solid var(--colorStrokeFocus2); }                       /* #1f282c フォーカス */
```

### 3.4 タイポグラフィ

```css
.body         { font: var(--fontWeightRegular) var(--fontSizeBase300)/var(--lineHeightBase300) var(--fontFamilyBase); }
                /* 14px / 20px / Segoe UI - 標準本文 */
.title        { font-size: var(--fontSizeBase600); line-height: var(--lineHeightBase600); font-weight: var(--fontWeightSemibold); }
                /* 24px / 32px / 600 - セクション見出し */
.hero         { font-size: var(--fontSizeHero900); line-height: var(--lineHeightHero900); }
                /* 40px / 52px - ヒーロータイトル */
.caption      { font-size: var(--fontSizeBase200); line-height: var(--lineHeightBase200); }
                /* 12px / 16px - キャプション */
.code         { font-family: var(--fontFamilyMonospace); }
                /* Consolas, Courier New */
```

### 3.5 角丸・スペーシング・影

```css
.card {
  background: var(--colorNeutralBackground1);
  border-radius: var(--borderRadiusMedium);   /* 4px - カード標準 */
  padding: var(--spacingVerticalL) var(--spacingHorizontalL); /* 16px */
  box-shadow: var(--shadow4);                  /* 軽い浮き */
}

.modal {
  border-radius: var(--borderRadiusXLarge);    /* 8px */
  box-shadow: var(--shadow28);                 /* 浮き強 */
}

.pill {
  border-radius: var(--borderRadiusCircular);  /* 完全な丸 */
}
```

### 3.6 アニメーション

```css
.button {
  transition:
    background-color var(--durationFaster) var(--curveEasyEase),    /* 100ms */
    transform var(--durationNormal) var(--curveDecelerateMid);      /* 200ms */
}
```

---

## 4. ステータス色（成功・警告・エラー）

| 種類 | 背景（弱） | 背景（強） | 文字色 | ボーダー |
|------|----------|----------|-------|---------|
| ✅ Success | `--colorStatusSuccessBackground1` (#f1faf1) | `--colorStatusSuccessBackground3` (#107c10) | `--colorStatusSuccessForeground1` (#0e700e) | `--colorStatusSuccessBorder1` |
| ⚠️ Warning | `--colorStatusWarningBackground1` (#fff9f5) | `--colorStatusWarningBackground3` (#f7630c) | `--colorStatusWarningForeground1` (#bc4b09) | `--colorStatusWarningBorder1` |
| ❌ Danger | `--colorStatusDangerBackground1` (#fdf3f4) | `--colorStatusDangerBackground3` (#c50f1f) | `--colorStatusDangerForeground1` (#b10e1c) | `--colorStatusDangerBorder1` |

```css
/* インラインアラート例 */
.alert-success {
  background: var(--colorStatusSuccessBackground1);
  color: var(--colorStatusSuccessForeground1);
  border-left: 4px solid var(--colorStatusSuccessBorder2);
  padding: var(--spacingVerticalM) var(--spacingHorizontalL);
  border-radius: var(--borderRadiusMedium);
}
```

---

## 5. ブランドカラー（Teal）の階層

提供 CSS は **SharePoint Online のデフォルトブランド `#038387`（Teal）** で生成されている。

```
#02494c ←→ #02767a ←→ #038387 ←→ #159196 ←→ #4bb4b7 ←→ #9bd9db ←→ #c7ebec ←→ #f0fafa
 Pressed   Hover      Base       Inverted   InvertedHv  Stroke2    InvertedSel Background2
```

**v9 の特徴**: ブランドカラーは 16 段階の `BrandVariants` から自動生成される。テナントのブランドカラー差し替えも公式 [Theme Designer](https://react.fluentui.dev/?path=/docs/theme-theme-designer--docs) でできる。

---

## 6. SharePoint カスタマイズでの実用パターン

### 6.1 sp-html-viewer / xsp-script-loader で使う

SharePoint ページ内では `.fui-FluentProvider*` がすでに DOM に存在するため、その配下なら CSS 変数をそのまま参照できる。

```css
/* ScriptLoader で注入する custom.css */
.my-custom-section {
  background: var(--colorNeutralBackground2);
  color: var(--colorNeutralForeground1);
  border: 1px solid var(--colorNeutralStroke1);
  border-radius: var(--borderRadiusMedium);
  padding: var(--spacingVerticalL) var(--spacingHorizontalL);
}

.my-custom-section a {
  color: var(--colorBrandForegroundLink);
  text-decoration: none;
}
.my-custom-section a:hover {
  color: var(--colorBrandForegroundLinkHover);
  text-decoration: underline;
}
```

### 6.2 SPFx Web パーツ（v8 + v9 トークン併用）

```tsx
// MyWebPart.module.scss
.container {
  // Fluent UI v9 の CSS 変数を直接利用（v8 コンポーネントでも見た目を v9 に揃えられる）
  background: var(--colorNeutralBackground1, #ffffff);
  color: var(--colorNeutralForeground1, #37474f);
  font-family: var(--fontFamilyBase);

  // フォールバック値（SharePoint 外で動かす Storybook 等のため）も書いておくのが安全
}
```

### 6.3 PCF（Power Apps Component Framework）

PCF では `context.fluentDesignLanguage.tokenTheme` から `Theme` オブジェクトを取得し、`<FluentProvider theme={myTheme}>` で囲むのが公式推奨。

```tsx
const myTheme = context.fluentDesignLanguage.tokenTheme;
return (
  <FluentProvider theme={myTheme}>
    {/* 自動的にホスト環境のテーマが適用される */}
  </FluentProvider>
);
```

参考: [Style components with modern theming - Microsoft Learn](https://learn.microsoft.com/en-us/power-apps/developer/component-framework/fluent-modern-theming)

---

## 7. アンチパターン（やってはいけないこと）

| ❌ NG | ✅ OK | 理由 |
|------|------|------|
| `color: #37474f;` | `color: var(--colorNeutralForeground1);` | テナント側でブランド変更すると壊れる |
| `padding: 16px;` | `padding: var(--spacingHorizontalL);` | 一貫性が崩れる |
| `border-radius: 4px;` | `border-radius: var(--borderRadiusMedium);` | 同上 |
| `transition: all 0.3s;` | `transition: ... var(--durationSlow) var(--curveEasyEase);` | アニメ感が SharePoint UI と揃わない |
| ホバー色を勝手に作る | `--colorBrandBackgroundHover` を使う | アクセシビリティコントラスト比が崩れる |
| v9 React を SPFx に持ち込む | v8 React + v9 CSS 変数 | バンドル肥大化・React 重複ロード |

---

## 8. テーマ切り替え（ライト・ダーク）

提供 CSS は **ライトテーマのみ**。ダーク／ハイコントラストでは値が変わるため、ハードコードせず変数で書いておけば自動追従する。

| テーマ | FluentProvider |
|-------|---------------|
| ライト | `webLightTheme` |
| ダーク | `webDarkTheme` |
| ハイコントラスト | `teamsHighContrastTheme` |

```tsx
import { FluentProvider, webLightTheme, webDarkTheme } from '@fluentui/react-components';

<FluentProvider theme={isDark ? webDarkTheme : webLightTheme}>
  <App />
</FluentProvider>
```

---

## 9. 実装チェックリスト

SharePoint カスタマイズで Fluent UI と整合させる際のチェック:

- [ ] 色は **すべて CSS 変数** で指定している（ハードコード `#xxx` がない）
- [ ] スペーシングは `--spacingHorizontal*` / `--spacingVertical*` を使っている
- [ ] 角丸は `--borderRadius*` を使っている
- [ ] フォントファミリーは `--fontFamilyBase` を継承している
- [ ] ホバー・プレス・選択状態に対応する `*Hover` / `*Pressed` / `*Selected` トークンを使っている
- [ ] アクセシビリティが必要な線は `--colorNeutralStrokeAccessible` を使っている
- [ ] フォーカスリングは `--colorStrokeFocus1` / `--colorStrokeFocus2` を使っている
- [ ] アニメーションは `--duration*` + `--curve*` を組み合わせている

---

## 10. 参考リソース

### 公式ドキュメント

- [Fluent UI React v9 公式](https://react.fluentui.dev/) — コンポーネント・トークン一覧
- [Fluent UI Theme Designer](https://react.fluentui.dev/?path=/docs/theme-theme-designer--docs) — ブランドカラーから16段階生成
- [Fluent UI Web Components Design Tokens - Microsoft Learn](https://learn.microsoft.com/en-us/fluent-ui/web-components/design-system/design-tokens)
- [Style components with modern theming (PCF) - Microsoft Learn](https://learn.microsoft.com/en-us/power-apps/developer/component-framework/fluent-modern-theming)
- [Fluent UI GitHub](https://github.com/microsoft/fluentui)

### コミュニティ

- [SPFx で v9 を使う際の問題 - N8D](https://n8d.at/the-fluent-ui-react-problem-in-sharepoint-framework/)
- [PCF + Fluent UI v9 テーミング - Dianamics PCF Lady](https://dianabirkelbach.wordpress.com/2024/01/13/standard-or-custom-theming-for-pcf-using-fluent-ui-v9/)
- [hTWOo](https://h2o.pnp.community/) — PnP コミュニティの SharePoint UI 互換 CSS フレームワーク（v9 トークン互換）

### ナレッジ内関連

- [[2026-04-24_SharePointで403が返る原因]]（あれば）
- AkaMaCorp `agents/05_sharepoint/skills/xsp-script-loader/` — JS/CSS 注入の実装スキル
- AkaMaCorp `agents/05_sharepoint/skills/sp-html-viewer/` — HTML 配信パターン
- AkaMaCorp `agents/04_designer/skills/brand-guide/` — デザイントークン全般
- AkaMaCorp `agents/04_designer/skills/design-handoff/` — 開発向け仕様書

---

## 関連

- [[notes-README]]
- [[AkaMaCorp-README]]
