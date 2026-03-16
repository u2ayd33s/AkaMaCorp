---
name: figma-to-code
description: Figma デザインから HTML/CSS/JavaScript コードを生成するスキル。"Figma", "デザインをコードに", "デザイン実装", "figma.com のURL" などが出たら使用する。Figma MCP サーバーと連携し、デザインの視覚的忠実度を保ちながらプロジェクトのスタック・コンポーネントに適合したコードを出力する。
---

# Figma → Code 変換

Figma デザインを本番品質の HTML/CSS/JavaScript に変換します。

## 基本フロー

1. **Figma URL を受け取る** — fileKey と nodeId を抽出する
2. **`get_design_context` を実行** — デザイン情報・スクリーンショット・コードヒントを取得
3. **プロジェクトの既存コンポーネントを確認** — 再利用できるものは再利用する
4. **コード生成** — デザインに忠実かつプロジェクトのスタックに合わせて実装
5. **確認・調整** — スクリーンショットと比較して差異があれば修正

## URL パース規則

```
figma.com/design/:fileKey/:name?node-id=:nodeId
→ nodeId の "-" を ":" に変換して get_design_context に渡す

figma.com/board/:fileKey/:name
→ FigJam ファイル、get_figjam を使用
```

## 出力コード方針

- **既存コンポーネント優先**: プロジェクトに同等コンポーネントがあれば新規生成しない
- **デザイントークン適用**: CSS カスタムプロパティ・変数を使用
- **レスポンシブ対応**: モバイルファーストで実装
- **生の hex カラー・絶対配置はスクリーンショットから判断**

## SharePoint ScriptLoader との組み合わせ

sharepoint-scriptloader プロジェクトで使用する場合:
- グローバルスタイルは `global.css` に追記
- ページ固有スタイルは `SitePages__<ページ名>.css` に記述
- JS はイベントリスナーを `DOMContentLoaded` でラップする

## Code Connect 対応

プロジェクトに Code Connect 設定がある場合は `get_code_connect_map` で確認し、
マッピング済みコンポーネントを優先使用する。
