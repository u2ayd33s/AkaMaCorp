---
name: aタグ別タブリンクは3属性セットで記述する
description: 別タブを開くリンクには target="_blank" / rel="noopener noreferrer" / data-interception="off" の3つをセットで記述するルール
type: feedback
---

`<a target="_blank">` を書く場合は必ず `rel="noopener noreferrer"` と `data-interception="off"` もセットで付ける。

**Why:** `rel="noopener"` はリバースタブナブジング脆弱性を防ぐ。`rel="noreferrer"` はリファラー漏洩を防ぐ（かつ noopener の効果も含む）。`data-interception="off"` は SharePoint の SPFx クライアントサイドルーターによるリンクインターセプトを無効化し、意図した URL へ正しく遷移させる。プロジェクトの構築ルールとして DevelopmentManual.md に明記済み。

**How to apply:** JS でテンプレートリテラルや innerHTML で `<a>` を生成するときも同様。`target="_blank"` を見たら3属性セットで書く。
