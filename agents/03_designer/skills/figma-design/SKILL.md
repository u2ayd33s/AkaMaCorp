# Skill: figma-design

## 概要

Figma MCP を活用してデザインの制作・取得・管理を行う。
ビジュアルデザインの作成からコード化まで Figma を中心に運用する。

## 使用場面

- Figmaデザインのコンテキスト・スクリーンショットを取得したいとき
- Figmaコンポーネントの詳細情報を確認したいとき
- FigmaデザインをHTML/CSS/JSに変換したいとき
- Figmaにデザインを書き込みたいとき

## Figma MCP ツール一覧

| ツール | 用途 |
|--------|------|
| `get_design_context` | デザインノードのコード・スクリーンショット・ヒントを取得 |
| `get_screenshot` | 指定ノードのスクリーンショットを取得 |
| `get_metadata` | ファイル・ページのメタデータ取得 |
| `get_figjam` | FigJam ボードの内容取得 |
| `get_variable_defs` | Figma 変数（デザイントークン）の定義を取得 |
| `generate_diagram` | FigJam にダイアグラムを生成 |
| `use_figma` | Figmaにデザイン要素を書き込む（figma-use スキル必須） |

## URL 形式

```
figma.com/design/:fileKey/:fileName?node-id=:nodeId
```

- `fileKey`: ファイルの一意識別子
- `nodeId`: ノードID（URLの `-` を `:` に変換して使用）

## 手順（デザイン取得）

1. FigmaのURLからfileKeyとnodeIdを抽出
2. `get_design_context` でデザイン情報を取得
3. スクリーンショットで視覚的に確認
4. デザイントークン（変数）は `get_variable_defs` で取得

## 手順（デザイン書き込み）

`use_figma` ツールを呼び出す前に必ず `figma-use` スキルをロードすること。

## デザインからコードへの変換

`get_design_context` の出力（React + Tailwind）はあくまで参考。
プロジェクトのスタック（HTML/CSS + Fluent UI 変数等）に合わせて変換する。

- Code Connect が設定されている場合 → マップされたコンポーネントを使用
- デザイントークン → プロジェクトの CSS 変数にマッピング
- レイアウト → プロジェクト既存のパターンを優先

## 注意事項

- `use_figma` は必ず `figma-use` スキルをロードしてから呼び出す
- Figma MCP はローカル Figma アプリの起動が必要
- ブランチ使用時は branchKey を fileKey として使用する
