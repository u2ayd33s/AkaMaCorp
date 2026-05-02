---
updated: 2026-05-02
tags:
  - spfx
  - sharepoint
---

# SPFx 環境構築・プロジェクト生成・プレビュー手順

## 環境構築

### 必要ツール（グローバルインストール）

```bash
npm install -g @microsoft/generator-sharepoint yo gulp-cli
```

- Node.js v18〜v20 推奨
- yo・gulp-cli はグローバル、プロジェクト固有の gulp 本体は node_modules 内に入る

## プロジェクト生成

```bash
mkdir <プロジェクト名> && cd <プロジェクト名>
yo @microsoft/sharepoint
```

### 対話形式の選択肢

| 質問 | 選択 |
|------|------|
| Which type of client-side component? | WebPart |
| Web part name | 任意 |
| Which template? | **No framework**（フレームワークなし） |

生成後に `npm install` を実行。

## ローカルプレビュー（gulp serve）

### serve.json の設定（必須）

`config/serve.json` の `{tenantDomain}` を実際のテナントに書き換える：

```json
{
  "port": 4321,
  "https": true,
  "initialPage": "https://<テナント>.sharepoint.com/_layouts/workbench.aspx"
}
```

### 起動

```bash
gulp serve
```

→ SharePoint Online の workbench が自動で開く
→ 証明書警告は「詳細設定 → localhost にアクセス」で続行

> [!warning] 注意
> 新しい SPFx ではローカル workbench（`localhost:4321/temp/workbench.html`）は廃止。
> SharePoint Online の workbench を使用する必要がある。

## パッケージ化・デプロイ

```bash
gulp bundle --ship
gulp package-solution --ship
```

### EPERM エラーが出た場合

```bash
rm -rf sharepoint/solution/debug
gulp package-solution --ship
```

> OneDrive が debug フォルダをロックすることがある。手動削除で解決。

### デプロイ手順

1. `sharepoint/solution/<名前>.sppkg` を確認
2. SharePoint 管理センター → アプリ → アプリカタログ
3. `.sppkg` をアップロード →「このソリューションを信頼する」
4. サイトコンテンツ → アプリの追加 → Web パーツを追加
