---
date: 2026-04-24
tags:
  - agent/designer
  - agent/sharepoint
  - type/📚reference
  - project/sharepoint
  - project/designer
---

# AI生成デザインの「没個性化」問題と脱却ガイド

Adrian Krebs 氏のブログ記事「[Show HN submissions tripled and now mostly share the same vibe-coded look](https://www.adriankrebs.ch/blog/design-slop)」(2026年4月) を踏まえ、AI 生成によく見られる没個性的デザインパターンと、その回避方法・プロンプト例をまとめます。

---

## 1. 背景 — なぜ今「AI デザイン slop」が話題なのか

- Claude Code などのコーディングエージェントの普及で、Hacker News の Show HN 投稿数は数年で3倍近くに増加。モデレーターが新規アカウントの投稿を制限せざるを得ないほど。
- Krebs 氏が Show HN 500件のランディングページを Playwright で自動スコアリングしたところ:
  - **Heavy slop(5つ以上のパターン該当): 21%**
  - **Mild(2〜4): 46%**
  - **Clean(0〜1): 33%**
- 全体の約 **3分の2** が「AIっぽい」デザイン特徴を複数備えている、という結果に。
- 問題の本質は「醜い」ことではなく、**全部が似ている = 記憶に残らない = ブランドとして機能しない** という点。

> 「colored left borders are almost as reliable a sign of AI-generated design as em-dashes for text」
> — ある現役デザイナー(Krebs 氏が引用)

---

## 2. AI 生成デザインによくある没個性パターン

Krebs 氏がデザイナーたちに聞き取った「AI 臭いサイン」を4カテゴリに整理します。

### 2.1 フォント

- **Inter をあらゆる用途に使用** — 特にセンタリングされたヒーロー見出し
- **LLM 定番のフォント組み合わせ**: Space Grotesk / Instrument Serif / Geist の三種の神器
- **ヒーロー見出しの1単語だけ Serif italic** にするアクセント手法

### 2.2 カラー

- **"VibeCode Purple"** — 紫系グラデーションの濫用
- **常時ダークモード** + ミディアムグレーの本文 + ALL-CAPS のセクションラベル
- ダークテーマで**本文のコントラスト比がギリギリ**
- **何にでもグラデーション**
- **大きな色付き glow / colored box-shadow**

### 2.3 レイアウト

- 汎用 sans-serif でセンタリングされたヒーロー
- **H1 の真上に badge / eyebrow pill**
- カード上端/左端の**カラード・ボーダー**
- 上にアイコンを配置した**均一なフィーチャーカードのグリッド**
- **「1, 2, 3」の番号付きステップシーケンス**
- **統計バナー行**(10万ユーザー / 99.9% Uptime 等の横並び)
- **絵文字アイコン付きサイドバー/ナビ**
- ALL-CAPS の見出しとセクションラベル

### 2.4 CSS / 実装

- **shadcn/ui そのまま**
- **グラスモーフィズム**(半透明+blur のカード)

> 具体的な回避テクニックは [[design-craft/SKILL|design-craft]] を参照。

---

## 3. なぜ LLM はこれらのパターンに収束するのか

| 要因 | 説明 |
|---|---|
| 訓練データの偏り | 2022〜2024年頃の SaaS ランディングページが大量に学習され、その平均が出力される |
| エコシステムの既定値 | shadcn/ui、Tailwind のデフォルトカラー、Vercel の Geist などがワンコマンドで入る |
| 「無難さ」への最適化 | LLM はリスクを取らず、多数派のパターンを再生産する |
| 指示の抽象度 | 「モダンでクリーンに」という曖昧な指示が、統計的中央値(= Inter + 紫グラデ)を引き出す |

---

## 4. 脱 slop のための解決策

### 4.1 設計思考レベル — 「極端に振る」勇気を持つ

無難な中央値を避けるには、**美的方向性を1つ選び、それを徹底する**ことが重要です。以下はその方向性の例(これらの中から選び、そこから更に独自色を加える):

- Brutally minimal(徹底的ミニマル)
- Maximalist chaos(過剰装飾)
- Retro-futuristic(レトロフューチャー)
- Organic / natural(有機的)
- Luxury / refined(高級感)
- Playful / toy-like(玩具的)
- Editorial / magazine(雑誌レイアウト)
- Brutalist / raw(ブルータリズム)
- Art deco / geometric(幾何学的)
- Soft / pastel(パステル)
- Industrial / utilitarian(工業的)

**重要なのは強度ではなく意図**。洗練されたミニマリズムも、過剰なマキシマリズムも、方向性が明確なら機能します。

### 4.2 タイポグラフィ

> トークン化・フォント体系の定義は [[brand-guide/SKILL|brand-guide]] を参照。

避けるべき:
- Inter / Roboto / Arial / システムフォント
- Space Grotesk(LLM が過剰に選ぶため、今は「あえて避ける」選択肢)
- Geist(Vercel エコシステムの既定値)

推奨アプローチ:
- ディスプレイ用とボディ用で**異なる性格のフォント**を組み合わせる
- 無料なら Google Fonts の中でも使用頻度の低いもの: Fraunces, Syne, Redaction, Monaspace, JetBrains Mono(見出しに使う等の転用), Fustat, Crimson Pro, EB Garamond, Bricolage Grotesque, Hanken Grotesk
- 有料/ライセンス費用が許すなら: PP Editorial New, GT America, Söhne, ABC Diatype, Neue Haas Grotesk 等
- 日本語サイトなら: Noto Sans JP 一択にせず、游ゴシック、IBM Plex Sans JP、Zen Kaku Gothic New、Shippori Mincho、Klee One など

### 4.3 カラー

- **紫グラデーションを禁じ手にする**(少なくとも最初の提案では)
- ドミナントカラー + 鋭いアクセント1〜2色。中途半端に均等配分しない
- ダークモード一択ではなく、**ライト/ダーク双方を真剣に設計**する
- 本文テキストは WCAG AA(4.5:1)を最低ライン、AAA(7:1)を目標に（→ [[a11y-check/SKILL|a11y-check]]）
- グラデーションは使うなら**意図を持って**(noise texture を重ねる、要素の境界を活かす等)

### 4.4 レイアウト

> CSS Grid/Flexbox による実装は [[css-layout/SKILL|css-layout]] を参照。

脱パターンのための具体策:
- センタリングヒーロー一辺倒をやめ、**左寄せ・非対称・ダイアゴナル構成**を検討
- H1 の上の pill badge を外し、代わりに**番号、日付、カテゴリ、手書き風注釈**など別の情報階層を試す
- フィーチャーは均一カードではなく、**重み付きレイアウト**(大小混在、図版/数字/文章の混成)で
- 統計バナーは**1〜2個の大きな数字を物語と共に**見せる方が印象に残る
- 絵文字アイコンを**カスタム SVG ピクトグラム / 写真 / 3D レンダリング**に置き換える

### 4.5 ディテール・質感

atmosphere と depth を作るための要素:
- ノイズ・グレイン・テクスチャのオーバーレイ
- カスタムカーソル、マイクロインタラクション
- スクロール連動の staggered reveal アニメーション(`animation-delay`)
- 手描き SVG、装飾ボーダー、パターン塗り
- ドロップシャドウを劇的に使う(ネオブルータリズムの硬いシャドウ等)

---

## 5. プロンプト例 — LLM に slop を吐かせない書き方

### 5.1 NG プロンプト(平均値が出る)

```
モダンでクリーンな SaaS のランディングページを作って。
ヒーロー、機能紹介、料金表を含めて。
```
→ 結果: Inter + 紫グラデ + badge + 3カラム均一カード + ALL-CAPS セクションラベル の典型 slop

### 5.2 改善プロンプトの構造

以下の5要素を明示的に指定すると、中央値から逃れやすくなります:

1. **美的方向性**(brutalist / editorial / retro-futuristic 等を1つ指名)
2. **禁止事項リスト**(Inter / 紫グラデ / badge / shadcn デフォルト等)
3. **タイポ指定**(具体的フォント名2種)
4. **カラーパレット**(具体的 hex 値3〜5色、ドミナントと明記)
5. **参考の反面教師**(「Linear のコピーにならないで」等)

### 5.3 プロンプト例: エディトリアル調

```
技術書出版社のランディングページを、1970年代の雑誌エディトリアルを
参照した美的方向性で作ってください。

【絶対に避けること】
- Inter / Space Grotesk / Geist の使用
- 紫系グラデーション
- H1 の上の pill badge
- センタリング一辺倒のヒーロー
- 均一な3カラムのフィーチャーカード
- ALL-CAPS の section label
- 絵文字アイコン
- shadcn/ui のデフォルト外観
- グラスモーフィズム

【タイポグラフィ】
- 見出し: Fraunces(太め、オプティカルサイズ大)
- 本文: Crimson Pro または EB Garamond

【カラーパレット】
- ドミナント: off-white #F5F1E8(紙色)
- テキスト: #1A1A1A
- アクセント: 朱赤 #C8372D のみ。グラデなし

【レイアウト】
- 左寄せ、2カラムのグリッド、時折3カラムに割れる
- 本文に drop cap(段落冒頭の大きな文字)
- ページ番号風の装飾を配置
- 罫線を積極的に使う
```

### 5.4 プロンプト例: ブルータリズム調

```
開発者向けCLIツールのサイトを、Neo-brutalism + Swiss grid で作って。

【禁じ手】
Inter, Geist, 紫グラデ, glow, 丸い大きなボタン, badge,
番号付きステップ, shadcn の見た目, グラスモーフィズム

【タイポ】
- 見出し: Space Mono(巨大に、文字詰めしっかり)
- 本文: IBM Plex Sans

【カラー】
- 背景 #EFEFEF、前景 #000、アクセントに #FF4500 1色のみ
- グラデ禁止、ベタ塗りのみ

【実装】
- ボタン・カードは 2px の黒い実線ボーダー
- ドロップシャドウは 4px 4px 0 0 #000 の硬いシャドウ
- ホバーで shadow が消え、要素が translate(4px, 4px) で動く
```

### 5.5 プロンプト例: オーガニック調

```
植物由来食品ブランドのサイトを、有機的で手触りのある
美的方向性で作ってください。

【禁止】
Inter, 紫グラデ, ダークモード, 均一カード, 絵文字アイコン,
ALL-CAPS ラベル, glow, shadcn 既定値

【タイポ】
- 見出し: Fraunces(ソフトネスを最大)または Migra
- 本文: Signifier または Söhne

【カラー】
- ベース: 薄いクリーム #F7F2E9
- 深緑 #2D4A2B, テラコッタ #B85C3E, 土色 #8B6F47
- グラデは使うが、水彩風のノイズテクスチャを重ねる

【ディテール】
- 不揃いな手書き SVG ストローク
- 写真は多めに、色味を統一
- セクション境界に有機的な波形 SVG
- スクロールで緩やかに要素がフェードイン
```

---

## 6. チェックリスト — 自分のサイトが slop に該当していないか

> 設計フェーズでの AI 回避基準は [[design-standards/SKILL|design-standards]] も参照。

出力後、以下15項目に照らして自己点検。**5項目以上 YES なら要リファクタ**。

- [ ] ヒーロー見出しが Inter またはその類似 sans-serif
- [ ] フォント組み合わせが Space Grotesk / Instrument Serif / Geist のいずれか
- [ ] 1単語だけ serif italic のアクセント手法を使っている
- [ ] 紫系グラデーション(VibeCode Purple)が存在する
- [ ] 常時ダークモード、ライトモードの検討なし
- [ ] ALL-CAPS の section label / eyebrow
- [ ] 本文のコントラスト比が 4.5:1 を下回る
- [ ] 色付き glow / colored box-shadow が多用されている
- [ ] H1 の真上に pill badge がある
- [ ] センタリングされたヒーロー構成
- [ ] カードの上端または左端にカラード・ボーダー
- [ ] 均一なアイコン付き3カラムフィーチャーカード
- [ ] 1, 2, 3 の番号付きステップシーケンス
- [ ] 絵文字をアイコンとして流用している
- [ ] shadcn/ui 既定値のまま + グラスモーフィズム

---

## 7. 考え方のまとめ

- **問題は AI ではなく「無思考な既定値の受け入れ」**。Bootstrap 時代にも同じことが起きていた。
- LLM の出力は**統計的中央値**。中央値を拒否する方向性指示 + 禁止事項リストが最も効果的。
- 「モダンでクリーン」は最も危険な指示。**具体的な美的方向性 + 具体的な禁じ手**で置き換える。
- 最終的には、**1つの忘れがたい要素**(フォント、配色、レイアウト、質感のどれか)を持たせることがブランドを作る。
- Krebs 氏の締めの問い — 「AI エージェントが web の主要ユーザーになったとき、デザインはどれだけ意味を持つのか」— は開かれたまま。ただし当面、人間が見る web では**差別化がますます価値を持つ**。

---

---

## 関連ノート

### エージェント
- [[agents/04_designer/CLAUDE|04_designer]] — デザイン担当エージェント（このナレッジの主要読者）
- [[agents/07_frontend/CLAUDE|07_frontend]] — フロントエンド実装担当

### スキル
- [[design-standards/SKILL|design-standards]] — AI制作回避チェックリスト・ベンチマーク基準
- [[design-craft/SKILL|design-craft]] — プロ品質UIテクニック集（タイポ・レイアウト・CSS）
- [[brand-guide/SKILL|brand-guide]] — カラー・タイポグラフィ・デザイントークン定義
- [[a11y-check/SKILL|a11y-check]] — WCAG 2.1 AA コントラスト比チェック
- [[css-layout/SKILL|css-layout]] — CSS Grid/Flexbox レイアウト設計

---

## 参考リンク

- Adrian Krebs, "Show HN submissions tripled and now mostly share the same vibe-coded look", 2026年4月20日
  <https://www.adriankrebs.ch/blog/design-slop>
- Hacker News `/showlim` 施策に関する議論
  <https://news.ycombinator.com/item?id=47300329>