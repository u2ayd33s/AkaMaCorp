# Skill: design-craft

## 概要

「AI が作ったように見えない」プロレベルのUIを作るための具体的テクニック集。
Steve Schoger (Tailwind Labs) の実践ノウハウを体系化。

すべての Web UI 制作時にこのスキルを参照し、各テクニックを適用すること。

---

## 1. タイポグラフィ

### Inter Variable フォント（最重要）

AI がデフォルトで生成する Inter は通常版。**必ず Variable + Display 版**を使う。

```html
<!-- rsms.me から Variable版を読み込む -->
<link rel="stylesheet" href="https://rsms.me/inter/inter.css">
<style>
  :root { font-family: 'Inter var', sans-serif; }
  @supports (font-variation-settings: normal) {
    :root {
      font-family: 'Inter var', sans-serif;
      font-feature-settings: 'cv01', 'cv02', 'cv03', 'cv04';
    }
  }
</style>
```

- `font-feature-settings: 'cv01'` → シングルストーリー a（`a` が丸い形に）
- Display 版 → `font-variation-settings: 'opsz' 32` で大きいテキストに最適化

### フォントウェイト: 中間値を使う

| 用途 | ウェイト | Tailwind 相当 |
|------|---------|--------------|
| 本文 | 400 | `font-normal` |
| UI テキスト | 450 | カスタム |
| **推奨ミドル** | **550** | **カスタム（medium と semibold の間）** |
| 強調 | 600 | `font-semibold` |
| ヘッドライン | 700 | `font-bold` |

```css
/* 550 ウェイトの指定 */
font-weight: 550;
/* または Tailwind で */
/* font-[550] */
```

### 大きいテキストのトラッキング

**24px 以上のテキストは必ずトラッキングを締める**。

```css
/* Tailwind */
tracking-tight   /* -0.025em */
tracking-tighter /* -0.05em  — ディスプレイ見出し用 */
```

### ライン高さ

| テキストサイズ | 推奨 line-height |
|-------------|----------------|
| 小 (14px) | 2.0 (28px) — `leading-loose` |
| 中 (16-18px) | 1.5-1.6 — `leading-relaxed` |
| 大 (24px+) | 1.1-1.2 — `leading-tight` |

### テキスト折り返し制御（CSS新機能）

```css
/* 末尾の孤立単語（オーファン）を防ぐ */
text-wrap: pretty;

/* 複数行テキストを均等な長さに揃える */
text-wrap: balance;
```

### テキスト幅を CH 単位で制御

```css
/* フォントサイズが変わっても1行の文字数を固定 */
max-width: 40ch; /* 約40文字幅 */

/* Tailwind */
max-w-[40ch]
```

---

## 2. ボーダー・リング・シャドウ

### ソリッドボーダー → 半透明アウターリングに置き換える

ソリッドカラーのボーダーはシャドウと重なると「泥っぽく」見える。

```css
/* ❌ 避ける */
border: 1px solid #e5e7eb;

/* ✅ 推奨: アウターリング gray-950 / 10% */
/* Tailwind */
ring ring-gray-950/10
/* または */
ring-1 ring-gray-950/10

/* shadow と組み合わせると自然な境界になる */
shadow-md ring-1 ring-gray-950/10
```

### インセットリング（単色背景のカード用）

```css
/* ✅ 単色（グレー等）の背景カードに使用 */
ring-inset ring-black/5

/* 通常のリングとの違い:
   通常: 要素の外側に描画（サイズに影響）
   インセット: 要素の内側に描画（サイズ不変）*/
```

### ボタンのインセットリング

```css
/* ボタンに border を使うと他ボタンとサイズが変わる → ring-inset を使う */
/* Tailwind */
ring-2 ring-inset ring-white/20
/* または Apple スタイル */
ring-1 ring-inset ring-white/10
```

---

## 3. ネスト角丸ルール（Nested Border Radius）

**外側の角丸 - パディング = 内側の角丸**

```
外側コンテナ: rounded-2xl (16px), padding: p-1 (4px)
→ 内側要素: rounded-xl (12px = 16 - 4)
```

| 外側 | パディング | 内側 |
|------|-----------|------|
| 24px (rounded-3xl) | 4px (p-1) | 20px (rounded-[20px]) |
| 16px (rounded-2xl) | 4px (p-1) | 12px (rounded-xl) |
| 12px (rounded-xl) | 4px (p-1) | 8px (rounded-lg) |

```html
<!-- 例: カード内スクリーンショット -->
<div class="rounded-2xl p-1 ring-1 ring-gray-950/10 shadow-md">
  <img class="rounded-xl" src="screenshot.png">
</div>
```

---

## 4. レイアウト設計

### 中央揃えを避ける

```css
/* ❌ AI デフォルト */
text-align: center;
margin: 0 auto;

/* ✅ 左揃え + 意図的なグリッド */
text-align: left;
```

### ヒーローの非対称レイアウト（3/5 + 2/5）

```html
<section class="grid grid-cols-5 gap-12 items-start">
  <!-- ヘッドライン: 3/5 -->
  <div class="col-span-3">
    <h1 class="text-5xl font-bold tracking-tighter leading-tight">
      収益をひとつの<br>ダッシュボードへ
    </h1>
  </div>
  <!-- サポートテキスト + CTA: 2/5 -->
  <div class="col-span-2 pt-2">
    <p class="text-base text-gray-600 text-pretty">説明文</p>
    <div class="mt-6 flex gap-3">
      <button>始める</button>
      <button>詳しく見る</button>
    </div>
  </div>
</section>
```

### アイブロウ（眉毛テキスト）は使わない

```html
<!-- ❌ 削除 -->
<span class="text-xs uppercase tracking-widest text-indigo-500">新機能</span>

<!-- ✅ ヘッドラインで直接語る -->
<h1>...</h1>
```

### コンテナ幅

```css
/* 標準的な幅 */
max-width: 1280px; /* Tailwind: max-w-7xl */
/* 狭いコンテンツ */
max-width: 1024px; /* Tailwind: max-w-5xl */
```

---

## 5. ボタン設計

### 標準スペック

```
高さ: 36 〜 38px（固定高さでなく padding で調整）
フォントサイズ: 14px
フォントウェイト: 550
角丸: rounded-full (pill) または rounded-lg
```

```html
<!-- Tailwind の例 -->
<button class="px-4 py-2 text-sm font-[550] rounded-full
               bg-gray-950 text-white
               ring-1 ring-gray-950/10
               hover:bg-gray-800 transition">
  今すぐ始める
</button>
```

### パディングベースのサイズ設定

```css
/* ❌ 固定高さ */
height: 38px;

/* ✅ パディングで調整（コンテンツ対応） */
padding: 0.5rem 1rem; /* py-2 px-4 */
```

---

## 6. スクリーンショット・画像

### 3x 解像度でキャプチャ

```
Mac: Cmd + Shift + 4 でキャプチャ後、3倍スケールで使用
Claude Code: `--scale 3` オプションでスクリーンショット取得
```

```html
<!-- retina 対応 -->
<img src="screenshot@3x.png"
     style="width: 100%; image-rendering: -webkit-optimize-contrast;">
```

### スクリーンショットの枠処理

```html
<!-- アウターリング + シャドウ -->
<div class="rounded-xl overflow-hidden shadow-2xl ring-1 ring-gray-950/10">
  <img src="app-screenshot@3x.png" class="w-full">
</div>
```

---

## 7. テスティモニアル（証言）カード

### 背景写真を使ったデザイン

```html
<div class="relative overflow-hidden rounded-2xl aspect-[4/5]">
  <!-- 背景写真 -->
  <img src="person.jpg" class="absolute inset-0 w-full h-full object-cover">
  <!-- グラデーションオーバーレイ -->
  <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent"></div>
  <!-- テキスト -->
  <div class="absolute bottom-0 p-6 text-white">
    <p class="text-lg font-[550]">"素晴らしいサービスです"</p>
    <p class="mt-2 text-sm text-white/70">— 田中 太郎, CEO</p>
  </div>
</div>
```

> ⚠️ AI 生成の人物写真は著作権・倫理リスクあり。実在の写真か、Unsplash 等の許諾済み素材を使用すること。

---

## 8. ロゴクラウド

```html
<!-- タイトルは不要 -->
<div class="flex items-center justify-between gap-8 opacity-60">
  <!-- 必ず SVG を使用（解像度独立・キレイ） -->
  <img src="logo-company-a.svg" class="h-6" alt="Company A">
  <img src="logo-company-b.svg" class="h-6" alt="Company B">
  <!-- ... -->
</div>
```

---

## 9. セクション間のキャンバスグリッド

```css
/* セクション区切りに細線を使い、テンプレート感を排除 */
.section-divider {
  border-top: 1px solid rgb(0 0 0 / 0.06);
}

/* Tailwind */
/* border-t border-black/[0.06] */
```

---

## 10. カラーパレット（AI 配色からの脱却）

```css
/* ❌ AI デフォルト */
--color-primary: #6366f1; /* indigo-500 */

/* ✅ ニュートラル起点 */
--color-primary: #0a0a0a;    /* gray-950 相当 */
--color-surface: #f5f5f5;   /* neutral-100 */
--color-border: rgb(0 0 0 / 0.10); /* /10 で柔らかく */

/* グレーは neutral を使う（slate より自然） */
/* tailwind: gray-* / neutral-* どちらも可だが統一すること */
```

---

## 適用優先度

すべてのデザインタスクで以下の順に確認する:

1. **Inter Variable フォント** — まず字体から整える
2. **アウターリング** — ボーダーをすべてリングに変換
3. **非対称レイアウト** — 中央揃えを解消
4. **ネスト角丸** — カード・画像の角丸を計算
5. **ボタンスペック** — 高さ・フォントを統一
6. **3x スクリーンショット** — 実際の UI 画面を使用
7. **テキスト折り返し** — pretty / balance を適用
