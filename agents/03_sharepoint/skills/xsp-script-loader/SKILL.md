---
name: xsp-script-loader
description: Artisan X-SP ScriptLoader（SPFx Application Customizer）を使ったSharePointカスタマイズ開発スキル。"スクリプトローダー", "ScriptLoader", "ページにJS/CSSを読み込む", "SiteAssets/ScriptLoader", "global.js", "ページ固有のスタイル", "SharePointでカスタムデザイン", "SPFxなしでカスタマイズ", "ページに処理を追加したい", "SharePointの見た目を変えたい" などが出たら積極的に使用する。SharePoint標準機能では実装できないデザイン・機能を、ページパスに対応したJS/CSSで実装するパターンをカバーする。
---

# xsp-script-loader 開発

SPFx Application Customizer「xsp-script-loader」を通じて、SharePoint の各ページに JS/CSS を自動注入するカスタマイズ開発を支援します。

> **詳細な仕様・コード例・デバッグ方法はすべて開発リポジトリの README を参照してください。**
> リポジトリ: `sharepoint-scriptloader` (https://github.com/u2ayd33s/sharepoint-scriptloader)

---

## 仕組みの概要

```
SharePoint サイト
  └─ SiteAssets/ScriptLoader/         ← ここにファイルを置くだけで自動読み込み
       ├── global.js / global.css      全ページ共通
       ├── SitePages__.js              SitePages 配下すべて
       ├── SitePages__フォルダ名__.js  特定フォルダ配下すべて
       └── SitePages__ページ名.js     特定ページのみ
```

**読み込み順（累積）**: global → ライブラリ全体 → フォルダ → 特定ページ

---

## 実装チェックリスト

- [ ] 対象スコープを確認（全ページ / ライブラリ / フォルダ / 特定ページ）
- [ ] ファイル名を命名規則に従って決定（`__` を忘れずに）
- [ ] JS は `window.registerSpLoaderFire()` で部分更新ページ遷移に対応
- [ ] `window.SPLoader.PageContext` / `window.SPLoader.UserProperties` でコンテキスト取得
- [ ] CSS はスコープを絞ったセレクタで標準 UI を保護
- [ ] `SiteAssets/ScriptLoader` にアップロード
- [ ] `?debug=console` でログ確認・動作確認

---

## パッケージ情報

| 項目 | 値 |
|------|-----|
| 製品名 | Artisan X-SP ScriptLoader |
| バージョン | 1.5.0 |
| Component ID | `fb680a2e-77cf-44c5-804a-ca2fa3764e9d` |
| Product ID | `3bef2208-7908-4438-b70e-cebeba7219d1` |
| デプロイ方式 | テナント全体展開（SkipFeatureDeployment: true） |
| パッケージ | `agents/sharepoint/xsp-script-loader/` |

---

## 更新履歴
- v1.0: 初版作成
- v1.1: 詳細仕様を sharepoint-scriptloader/README.md に移管・SKILL.md を簡潔化
