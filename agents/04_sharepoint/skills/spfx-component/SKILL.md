---
name: spfx-component
description: SPFx (SharePoint Framework) の Web パーツや拡張機能の雛形生成・開発を支援するスキル。"SPFx", "web パーツ", "SharePoint コンポーネント", "拡張機能", "Application Customizer", "Field Customizer", "Command Set" などのキーワードが出たら積極的に使用する。TypeScript ベースの SPFx プロジェクトのスキャフォールド、コンポーネント実装、プロパティパネル設計、ビルド・デプロイ手順もカバーする。
---

# SPFx コンポーネント開発

SPFx Web パーツ・拡張機能の設計から実装・デプロイまでを支援します。

## 対応範囲

- **Web パーツ**: React / No Framework / Knockout ベース
- **拡張機能**: Application Customizer, Field Customizer, ListView Command Set
- **プロパティパネル**: テキスト、ドロップダウン、トグル等のコントロール
- **データ取得**: PnPjs / SharePoint REST API / Microsoft Graph

## 作業フロー

### 1. 要件確認
ユーザーに以下を確認する:
- Web パーツ or 拡張機能のどちらか
- フレームワーク (React 推奨 / No Framework)
- 必要なプロパティ・データソース
- ターゲットの SharePoint バージョン (Online / 2019 / 2016)

### 2. スキャフォールド
```bash
yo @microsoft/sharepoint
```
プロジェクト名・コンポーネント名・フレームワーク選択を案内する。

### 3. コンポーネント実装

#### Web パーツの基本構造 (React)
```typescript
import * as React from 'react';
import { IWebPartProps } from './IWebPartProps';

const MyWebPart: React.FC<IWebPartProps> = ({ title, description }) => {
  return (
    <div className="webpart-container">
      <h2>{title}</h2>
      <p>{description}</p>
    </div>
  );
};

export default MyWebPart;
```

#### プロパティパネル設定
```typescript
protected getPropertyPaneConfiguration(): IPropertyPaneConfiguration {
  return {
    pages: [{
      header: { description: strings.PropertyPaneDescription },
      groups: [{
        groupName: strings.BasicGroupName,
        groupFields: [
          PropertyPaneTextField('title', { label: strings.TitleFieldLabel }),
          PropertyPaneTextField('description', { label: strings.DescriptionFieldLabel })
        ]
      }]
    }]
  };
}
```

### 4. ビルド・デプロイ
```bash
gulp bundle --ship   # 本番ビルド
gulp package-solution --ship  # .sppkg 生成
```
App Catalog へのアップロード手順も案内する。

## ベストプラクティス

- **型安全**: すべてのプロパティに TypeScript 型を定義する
- **PnPjs 活用**: REST API 直書きより PnPjs を推奨 (コードが簡潔)
- **環境変数**: テナント URL 等はハードコードせず `context.pageContext` から取得
- **エラーハンドリング**: API 呼び出しは必ず try/catch を入れる

## 更新履歴
- v1.0: 初版作成
