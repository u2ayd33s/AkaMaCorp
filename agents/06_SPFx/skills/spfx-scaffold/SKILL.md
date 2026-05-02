---
name: spfx-scaffold
description: Yeoman ジェネレーターによる SPFx プロジェクト雛形生成・Node.js バージョン確認・tsconfig / gulpfile / package.json の初期設定を行うスキル。新規 SPFx プロジェクト開始時に使用する。
---

# SPFx スキャフォールド

新規 SPFx プロジェクトの環境構築と雛形生成を行う。

## 前提確認

```bash
node -v   # SPFx 1.18.x → Node 18.x LTS
npm -v
yo --version
gulp --version
```

## プロジェクト生成

```bash
yo @microsoft/sharepoint
```

対話形式で以下を設定:
| 項目 | 推奨値 |
|------|--------|
| Solution name | ケバブケース（例: `my-webpart`） |
| SharePoint version | SharePoint Online only |
| Component type | WebPart / Extension |
| Framework | React |
| Component name | パスカルケース（例: `MyWebPart`） |

## 生成後の初期設定

```bash
npm install          # 依存関係インストール
npm install @pnp/sp @pnp/graph  # PnPjs 追加
gulp trust-dev-cert  # 開発用証明書信頼
gulp serve           # ローカルサーバー起動
```

## 更新履歴
- v1.0: 初版作成
