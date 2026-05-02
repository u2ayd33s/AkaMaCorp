---
name: spfx-deploy
description: SPFx ソリューションの本番ビルド（gulp bundle / gulp package-solution）と App Catalog へのデプロイ手順を提供するスキル。テナント全体展開・サイトコレクション展開の判断も含む。
---

# SPFx デプロイ

## ビルド〜パッケージング

```bash
# 本番ビルド
gulp bundle --ship

# .sppkg パッケージ生成
gulp package-solution --ship
# → sharepoint/solution/<ソリューション名>.sppkg が生成される
```

## App Catalog へのアップロード

1. SharePoint 管理センター → アプリ → アプリカタログ を開く
2. `sharepoint/solution/*.sppkg` をドラッグ＆ドロップ
3. 展開方式を選択:
   | 方式 | 用途 |
   |------|------|
   | テナント全体に展開 | 全サイトで即時利用可能にする場合 |
   | サイトコレクションに追加 | 特定サイトのみで使う場合 |

## バージョン管理

`package-solution.json` の `version` を毎回インクリメントする:
```json
{
  "solution": {
    "name": "my-solution",
    "version": "1.0.0.1"
  }
}
```

## CDN 配置（任意）

```bash
# SharePoint Online CDN を使う場合
gulp bundle --ship --cdnbasepath "https://<tenant>.sharepoint.com/sites/cdn/ClientSideAssets"
```

## 更新履歴
- v1.0: 初版作成
