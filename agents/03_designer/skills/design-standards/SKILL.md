# Skill: design-standards

## 概要

デザインの品質基準・ベンチマークを定義する。
このスキルは「何を目指すか」の羅針盤として、すべてのデザイン判断の基礎となる。

---

## ベンチマーク基準

### Web サイト制作
- **CSS Design Awards** — インタラクション・モーション・ビジュアルの最高峰事例
- **Awwwards 受賞レベル** — UX・タイポグラフィ・レイアウトの国際標準

「AI が作ったように見えない」デザインがゴール。
受賞サイトの共通点: 非対称レイアウト・精密なタイポグラフィ・意図的な余白・リアルなコンテンツ

### UI / コンポーネント設計
- **Apple Human Interface Guidelines (HIG)** — 空間デザイン・一貫性・直感的操作
  - 参照: https://developer.apple.com/design/human-interface-guidelines/
- **Google Material Design 3** — コンポーネント・モーション・カラーシステム
  - 参照: https://m3.material.io/

---

## AI制作っぽく見えるデザインの特徴（避けるべき）

以下の特徴が揃うと「AI生成デザイン」に見える:

| 問題 | 原因 | 対策 |
|------|------|------|
| すべて中央揃え | AI のデフォルト | 左揃え or 非対称レイアウト |
| インディゴ一択の配色 | AI のデフォルト配色 | neutral gray-950 / プロジェクト固有色 |
| セクションが同じパターンの繰り返し | テンプレート感 | セクションごとに構造を変える |
| フォントが平坦（Regular / Bold しかない） | 標準 Inter のみ | Inter Variable の中間ウェイト活用 |
| 偽のUI・偽のスクリーンショット | コンテンツ不足 | 実際のアプリ画面・実コンテンツを使用 |
| 角丸がすべて同じ | 深さがない | ネスト角丸ルールを適用 |
| ベタ塗りボーダー | デフォルト | 半透明リング（ring/10）に置き換え |

---

## 品質チェックリスト

デザイン完成前に以下を確認する:

### タイポグラフィ
- [ ] Inter Variable (display) を使用しているか
- [ ] ヘッドラインのトラッキングを締めているか（24px以上）
- [ ] 中間ウェイト（550等）を活用しているか
- [ ] `text-wrap: pretty` でオーファン防止しているか

### レイアウト
- [ ] 中央揃えだけに頼っていないか
- [ ] ヒーローが非対称レイアウトになっているか
- [ ] コンテナ幅は 1280px 程度か（狭すぎないか）
- [ ] セクションごとに構造が変わっているか

### ボーダー・シャドウ
- [ ] ソリッドカラーボーダーを使っていないか（→ `ring/10` に変換）
- [ ] ネスト要素で角丸計算をしているか

### コンテンツ
- [ ] 実スクリーンショット（3x解像度）を使用しているか
- [ ] ロゴは SVG を使用しているか
- [ ] 証言・数値は架空でも具体的に見えるか

---

## 参考リソース

- CSS Design Awards: https://www.cssdesignawards.com/
- Awwwards: https://www.awwwards.com/
- Apple HIG: https://developer.apple.com/design/human-interface-guidelines/
- Google Material 3: https://m3.material.io/
- Steve Schoger "Designing with Claude Code": https://youtu.be/lkKGQVHrXzE
- Tailwind CSS デザイン18の極意: https://www.youtube.com/watch?v=zHZgavnL2Dk
