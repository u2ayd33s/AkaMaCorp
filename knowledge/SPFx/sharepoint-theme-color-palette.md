# SharePoint Fluent UI v3 テーマカラーパレット分析

updated: 2026-05-03

## 分析対象テーマ

| ファイル | 種別 | カラー系統 | `--colorBrandBackground` |
|---|---|---|---|
| LightMode-1 | 自作 | Teal | `#038387` |
| LightMode-2 | 自作 | Blue | `#0097d0` |
| DarkMode-1 | 自作 | Cyan (Tealダーク版) | `#00e1ff` |
| DarkMode-2 | 自作 | Green | `#0ae448` |
| LightMode-3 | 標準 | DarkTeal | `#03787C` |
| LightMode-4 | 標準 | Blue | `#1267B5` |
| DarkMode-3 | 標準 | Blue | `#529FF1` |
| DarkMode-4 | 標準 | Teal | `#51AEB2` |

---

## ライト/ダーク切替の設計原則

### 原則1: Brand Color は Dark で明るくする

Light モードで使う暗い/落ち着いたブランドカラーをそのまま Dark に使うと背景と同化する。  
Dark モードでは **明るい/鮮やかなバリアント** を `--colorBrandBackground` に使う。

```
Light Teal: #038387 (暗め) → Dark Cyan: #00e1ff (明るいシアン)
Light Blue: #1267B5       → Dark Blue: #529FF1 or #79bfff (明るいブルー)
```

### 原則2: colorNeutralForegroundOnBrand の反転

Brand 背景上のテキスト色はライト/ダークで**反転**する。

| モード | Brand BG | OnBrand テキスト |
|---|---|---|
| Light | 暗め (#038387) | `#ffffff` (白) |
| Dark 自作 | 明るい (#00e1ff) | `#061e1f` (暗色) |
| Dark 標準 | 中間 (#529FF1) | `#242424` (暗色) |

### 原則3: Background は階層スケールで定義

Layer 番号が大きいほど暗い（ライト）/ 深い（ダーク）

| Layer | Light (自作) | Light (標準) | Dark 標準 | Dark 自作 Teal系 |
|---|---|---|---|---|
| BG1 | `#ffffff` | `#fff` | `#292929` | `#061e1f` |
| BG2 | `#f8f8f8` | `#fafafa` | `#1f1f1f` | `#092828` |
| BG3 | `#f4f4f4` | `#f5f5f5` | `#141414` | `#0d3031` |
| BG4 | `#f4f4f4` | `#f5f5f5` | `#0a0a0a` | `#0d3031` |
| BG5 | `#eaeaea` | `#f5f5f5` | `#000000` | `#133e3f` |
| BG6 | `#eaeaea` | `#f5f5f5` | `#000000` | `#133e3f` |

> 自作ダークテーマは純黒でなくテーマカラーを混ぜた背景にしている（より個性的な仕上がり）

### 原則4: Stroke(Border) の値域

| モード | Stroke1 (メイン) | Stroke2 (サブ) | StrokeAccessible |
|---|---|---|---|
| Light 自作 | `#d0d0d0` | `#dadada` | `#7b8c95` |
| Light 標準 | `#d1d1d1` | `#e0e0e0` | `#616161` |
| Dark 標準 | `#666666` | `#525252` | `#adadad` |
| Dark 自作 Teal | `#1d4e4f` | `#184748` | `#d0d0d0` |
| Dark 自作 Green | `#0b0c0c` | `#0b0d0c` | `#fffdee` |

> Dark 標準のボーダーは純グレー。Dark 自作はブランドカラーのトーンを混ぜる。

---

## カテゴリ別 推奨ピックアップ変数

### A. Primary / Brand カラー

```css
/* Brand ベース */
--colorBrandBackground           /* ボタン・アクセント背景 */
--colorBrandBackgroundHover      /* ホバー状態 */
--colorBrandBackgroundPressed    /* プレス状態 */
--colorBrandBackgroundSelected   /* 選択状態 */

/* Brand テキスト */
--colorBrandForeground1          /* Brand色テキスト（On Light BG） */
--colorBrandForeground2          /* Brand色テキスト サブ */
--colorBrandForegroundLink       /* Brand リンクテキスト */
--colorBrandForegroundLinkHover

/* Brand Border */
--colorBrandStroke1              /* Brand枠線（アクティブ選択等） */
--colorBrandStroke2              /* Brand枠線 サブ */

/* Brand Soft Background */
--colorBrandBackground2          /* ブランド色の薄い背景（Light: 極薄 / Dark: 極暗） */
```

### B. Background（階層別）

```css
/* ページ・コンポーネント背景 */
--colorNeutralBackground1        /* メイン白/ダーク背景 */
--colorNeutralBackground1Hover   /* インタラクティブ背景ホバー */
--colorNeutralBackground1Pressed
--colorNeutralBackground1Selected

--colorNeutralBackground2        /* セカンダリ背景（サイドパネル等） */
--colorNeutralBackground3        /* ターシャリ背景（ヘッダー等） */
--colorNeutralBackground5        /* 強調されたニュートラル背景 */
--colorNeutralBackground6        /* ボーダー的な塗り背景 */

/* カード */
--colorNeutralCardBackground     /* カードBG */
--colorNeutralCardBackgroundHover

/* 特殊 */
--colorSubtleBackground          /* 通常: transparent */
--colorSubtleBackgroundHover     /* ホバー時の薄い背景 */
--colorSubtleBackgroundPressed

/* Brand Soft */
--vivaConnectionsSoftBackground  /* Viva Connections ソフトBG */
/* Light: 極薄ブランド色 / Dark: 極暗ブランド色 */
```

### C. Foreground（テキスト）

```css
/* 本文テキスト */
--colorNeutralForeground1        /* メイン文字色 */
--colorNeutralForeground2        /* サブ文字色（補助テキスト） */
--colorNeutralForeground3        /* 三次テキスト（ラベル、ヒント） */
--colorNeutralForeground4        /* 四次テキスト */

/* 特殊テキスト */
--colorNeutralForegroundOnBrand  /* Brand背景上のテキスト ← ライト/ダーク反転する重要変数 */
--colorNeutralForegroundDisabled /* 無効化テキスト */
--colorNeutralForegroundInverted /* 反転テキスト（白/黒反転） */
--colorNeutralForegroundStaticInverted /* 常に反転（モード非依存） */

/* Brand テキスト on Neutral BG */
--colorBrandForeground1          /* ニュートラル背景上のブランド色テキスト */
--colorBrandForegroundInverted   /* Brand背景上の反転ブランド色テキスト */
```

### D. Border / Stroke

```css
/* ボーダー */
--colorNeutralStroke1            /* 標準ボーダー（Input, Card外枠等） */
--colorNeutralStroke2            /* やや薄いボーダー */
--colorNeutralStroke3            /* 薄いセパレーター */
--colorNeutralStroke4            /* セクションボーダー */
--colorNeutralStrokeSubtle       /* 極薄ボーダー */
--colorNeutralStrokeAccessible   /* アクセシビリティ対応ボーダー（コントラスト保証） */
--colorNeutralStrokeAccessibleSelected /* 選択時アクセシブルボーダー = Brand色 */

--colorNeutralStrokeOnBrand      /* Brand背景上のボーダー（通常 white/dark） */
--colorNeutralStrokeDisabled     /* 無効状態ボーダー */

/* Brand ボーダー */
--colorBrandStroke1              /* ブランドボーダー */
--colorCompoundBrandStroke       /* Compound (チェックボックス等) */
--colorCompoundBrandStrokeHover

/* フォーカスリング */
--colorStrokeFocus1              /* フォーカスリング外枠 */
--colorStrokeFocus2              /* フォーカスリング内枠 */
```

### E. Interactive States（ホバー・プレス）

```css
/* Compound Brand（チェックボックス・Toggle等） */
--colorCompoundBrandBackground   /* = colorBrandBackground */
--colorCompoundBrandBackgroundHover
--colorCompoundBrandBackgroundPressed
--colorCompoundBrandForeground1
--colorCompoundBrandForeground1Hover
--colorCompoundBrandForeground1Pressed
```

### F. Semantic（コンポーネント意味変数）

```css
/* プライマリボタン */
--semanticPrimaryButtonBackground        /* = colorBrandBackground */
--semanticPrimaryButtonBackgroundHovered
--semanticPrimaryButtonBackgroundPressed
--semanticPrimaryButtonText             /* = colorNeutralForegroundOnBrand */
--semanticPrimaryButtonTextHovered
--semanticPrimaryButtonTextPressed
--semanticPrimaryButtonBackgroundDisabled
--semanticPrimaryButtonTextDisabled

/* セカンダリボタン */
--semanticButtonBackground
--semanticButtonBackgroundHovered
--semanticButtonBackgroundPressed
--semanticButtonBorder
--semanticButtonTextHovered
--semanticButtonTextDisabled
--semanticButtonBackgroundDisabled

/* テキスト */
--semanticBodyText        /* = colorNeutralForeground1 */
--semanticActionLink      /* リンクテキスト */
--semanticErrorText       /* エラーテキスト */
--semanticDisabledText
--semanticDisabledBodyText
--semanticDisabledBodySubtext

/* 入力フォーム */
--semanticInputBackground /* = colorNeutralBackground1 */
--semanticInputText       /* = colorNeutralForeground1 */

/* 無効化状態 */
--semanticDisabledBackground
```

### G. Status Colors（共通・モード非依存）

Status カラーは全テーマでほぼ共通値。ダークでは前景/背景が反転する。

```css
/* Success (Green) */
--colorStatusSuccessBackground1     /* 極薄Green背景 */
--colorStatusSuccessBackground3     /* #107c10 (全Light共通) */
--colorStatusSuccessForeground1     /* テキスト */
--colorStatusSuccessBorder2         /* ボーダー */

/* Warning (Orange) */
--colorStatusWarningBackground1
--colorStatusWarningBackground3     /* #f7630c */
--colorStatusWarningForeground1
--colorStatusWarningBorder2

/* Danger / Error (Red) */
--colorStatusDangerBackground1
--colorStatusDangerBackground3      /* #c50f1f */
--colorStatusDangerForeground1
--colorStatusDangerBorder2
```

---

## テーマ別カラー早見表（主要変数のみ）

### Light Mode

| 変数 | LM-1 自作Teal | LM-2 自作Blue | LM-3 標準Teal | LM-4 標準Blue |
|---|---|---|---|---|
| `--colorBrandBackground` | `#038387` | `#0097d0` | `#03787C` | `#1267B5` |
| `--colorBrandForeground1` | `#038387` | `#0097d0` | `#03787C` | `#1267B5` |
| `--colorBrandForegroundLink` | `#02767a` | `#008abc` | `#00686d` | `#0058a4` |
| `--colorBrandBackground2` | `#f0fafa` | `#f3fafd` | `#d6ffff` | `#f0fdff` |
| `--colorNeutralBackground1` | `#ffffff` | `#ffffff` | `#fff` | `#fff` |
| `--colorNeutralBackground2` | `#f8f8f8` | `#f8f8f8` | `#fafafa` | `#fafafa` |
| `--colorNeutralBackground3` | `#f4f4f4` | `#f4f4f4` | `#f5f5f5` | `#f5f5f5` |
| `--colorNeutralForeground1` | `#37474f` | `#37474f` | `#242424` | `#242424` |
| `--colorNeutralForeground2` | `#7b8c95` | `#7b8c95` | `#424242` | `#424242` |
| `--colorNeutralForeground3` | `#818a8f` | `#818a8f` | `#616161` | `#616161` |
| `--colorNeutralForegroundOnBrand` | `#ffffff` | `#ffffff` | `#fff` | `#fff` |
| `--colorNeutralStroke1` | `#d0d0d0` | `#d0d0d0` | `#d1d1d1` | `#d1d1d1` |
| `--colorNeutralStroke2` | `#dadada` | `#dadada` | `#e0e0e0` | `#e0e0e0` |
| `--colorNeutralStrokeAccessible` | `#7b8c95` | `#7b8c95` | `#616161` | `#616161` |
| `--colorBrandStroke1` | `#038387` | `#0097d0` | `#03787C` | `#1267B5` |
| `--colorStrokeFocus1` | `#ffffff` | `#ffffff` | `#fff` | `#fff` |
| `--colorStrokeFocus2` | `#1f282c` | `#1f282c` | `#000000` | `#000000` |
| `--semanticBodyText` | `#37474f` | `#37474f` | `#242424` | `#242424` |
| `--semanticInputBackground` | `#ffffff` | `#ffffff` | `#fff` | `#fff` |
| `--semanticButtonBorder` | `#8a8886` | `#8a8886` | `#d1d1d1` | `#d1d1d1` |
| `--vivaConnectionsSoftBackground` | `#f0fafa` | `#f3fafd` | `#f0f9fa` | `#f3f8fc` |

### Dark Mode

| 変数 | DM-1 自作Cyan | DM-2 自作Green | DM-3 標準Blue | DM-4 標準Teal |
|---|---|---|---|---|
| `--colorBrandBackground` | `#00e1ff` | `#0ae448` | `#529FF1` | `#51AEB2` |
| `--colorBrandForeground1` | `#00e1ff` | `#0ae448` | `#79bfff` | `#72cdd1` |
| `--colorBrandForegroundLink` | `#19e4ff` | `#1fe657` | `#a2d4ff` | `#87e3e7` |
| `--colorBrandBackground2` | `#00090a` | `#000903` | `#001c52` | `#00272a` |
| `--colorNeutralBackground1` | `#061e1f` | `#0e100f` | `#292929` | `#292929` |
| `--colorNeutralBackground2` | `#092828` | `#0d0f0e` | `#1f1f1f` | `#1f1f1f` |
| `--colorNeutralBackground3` | `#0d3031` | `#0d0f0e` | `#141414` | `#141414` |
| `--colorNeutralForeground1` | `#ffffff` | `#fffce1` | `#ffffff` | `#ffffff` |
| `--colorNeutralForeground2` | `#d0d0d0` | `#fffdee` | `#d6d6d6` | `#d6d6d6` |
| `--colorNeutralForeground3` | `#d0d0d0` | `#fffdee` | `#adadad` | `#adadad` |
| `--colorNeutralForegroundOnBrand` | `#061e1f` | `#0e100f` | `#242424` | `#242424` |
| `--colorNeutralStroke1` | `#1d4e4f` | `#0b0c0c` | `#666666` | `#666666` |
| `--colorNeutralStroke2` | `#184748` | `#0b0d0c` | `#525252` | `#525252` |
| `--colorNeutralStrokeAccessible` | `#d0d0d0` | `#fffdee` | `#adadad` | `#adadad` |
| `--colorBrandStroke1` | `#00e1ff` | `#0ae448` | `#79bfff` | `#72cdd1` |
| `--colorStrokeFocus1` | `#061e1f` | `#0e100f` | `#000000` | `#000000` |
| `--colorStrokeFocus2` | `#f8f8f8` | `#fffffb` | `#ffffff` | `#ffffff` |
| `--semanticBodyText` | `#ffffff` | `#fffce1` | `#ffffff` | `#ffffff` |
| `--semanticInputBackground` | `#061e1f` | `#0e100f` | `#292929` | `#292929` |
| `--semanticButtonBorder` | `#d0d0d0` | `#fffdee` | `#666666` | `#666666` |
| `--vivaConnectionsSoftBackground` | `#00090a` | `#000903` | `#193049` | `#193536` |

---

## カスタムテーマ設計ガイド

### Light → Dark の変換ルール

1. **Brand色**: 彩度を上げ・明度を上げる (Teal #038387 → Cyan #00e1ff)
2. **背景**: 白系 → ダーク系 (Neutral: #fff → #292929、カスタム: テーマ色混じり暗色)
3. **テキスト**: 暗色 → 白 (#37474f or #242424 → #ffffff)
4. **ボーダー**: 薄グレー → 中間グレー (#d0d0d0 → #666666)
5. **OnBrand**: 白 → 暗色 (ブランドBGが明るくなるため)
6. **Shadow**: 不透明度を増やす (0.12/0.14 → 0.24/0.28)
7. **BrandBackground2**: 極薄 → 極暗 (#f0fafa → #00090a)

### よく使う変数グループ（コピペ用）

#### 背景・テキスト基本4変数
```css
/* これだけでライト/ダーク対応の大半をカバー */
background: var(--colorNeutralBackground1);
color: var(--colorNeutralForeground1);
border-color: var(--colorNeutralStroke1);
accent-color: var(--colorBrandBackground);
```

#### ボタン
```css
/* Primary Button */
background: var(--semanticPrimaryButtonBackground);
color: var(--semanticPrimaryButtonText);

/* Default Button */
background: var(--semanticButtonBackground);
border: 1px solid var(--semanticButtonBorder);
color: var(--colorNeutralForeground1);

/* Hover */
background: var(--semanticButtonBackgroundHovered);
color: var(--semanticButtonTextHovered);
```

#### 入力フォーム
```css
background: var(--semanticInputBackground);
color: var(--semanticInputText);
border: 1px solid var(--colorNeutralStroke1);

/* Focus ring */
outline: var(--strokeWidthThick) solid var(--colorStrokeFocus2);
outline-offset: var(--strokeWidthThin);
```

#### カード
```css
background: var(--colorNeutralCardBackground);
border: 1px solid var(--colorNeutralStroke2);
box-shadow: var(--shadow4);
```

#### Brand アクセント（テキスト・アイコン）
```css
/* ニュートラル背景上のブランド色要素 */
color: var(--colorBrandForeground1);

/* ブランド背景のコンテナ */
background: var(--colorBrandBackground);
color: var(--colorNeutralForegroundOnBrand); /* ← 自動反転 */
```

#### Soft Brand Area（淡いブランド背景）
```css
background: var(--colorBrandBackground2);
/* または */
background: var(--vivaConnectionsSoftBackground);
```

---

## 注意点・落とし穴

### 1. 自作ダークテーマの Stroke は極端に暗い
DM-2 (Green) の `--colorNeutralStroke1: #0b0c0c` は背景 `#0e100f` とほぼ同色。  
ボーダーが必要な場面では `--colorNeutralStrokeAccessible` (`#fffdee`) を使うこと。

### 2. 自作 Light テーマのテキストは Blue-Gray 系
LM-1/LM-2 の `--colorNeutralForeground1: #37474f` は標準の `#242424` より薄め。  
アクセシビリティが要件の場合 `--colorNeutralStrokeAccessible` でコントラスト確認推奨。

### 3. colorBrandBackground2 の用途
- Light: `#f0fafa`（極薄ブランド薄背景） → ハイライト・選択状態背景に使える
- Dark: `#00090a`（ほぼ黒） → ほぼ見えないので装飾的な濃淡付けに限定

### 4. Shadow はモードで強度が変わる
- Light: `rgba(0,0,0,0.12)` + `rgba(0,0,0,0.14)`
- Dark Standard: `rgba(0,0,0,0.24)` + `rgba(0,0,0,0.28)` （ほぼ2倍）
- 自作: Light と同じ値（0.12/0.14）→ 視覚的に影が弱く出る可能性あり

---

## 関連ファイル

- `reference_fluent_ui3_css.md` - Fluent UI v3 全CSS変数一覧（Tealテーマ）
- テーマ原本: `C:\Users\u2ayd\Desktop\LightMode-1〜4.txt`, `DarkMode-1〜4.txt`
