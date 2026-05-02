---
name: spfx-component
description: SPFx Web パーツ・拡張機能のコンポーネント実装（React / Fluent UI v9 / PnPjs）を支援するスキル。Web パーツ、Application Customizer、Field Customizer、ListView Command Set の実装パターンを提供する。
---

# SPFx コンポーネント実装

## Web パーツ（React + Fluent UI v9）

```typescript
import * as React from 'react';
import { Text, Button } from '@fluentui/react-components';
import { spfi, SPFx } from '@pnp/sp';
import '@pnp/sp/webs';
import '@pnp/sp/lists';

interface IMyWebPartProps {
  title: string;
  context: WebPartContext;
}

const MyWebPart: React.FC<IMyWebPartProps> = ({ title, context }) => {
  const [items, setItems] = React.useState<unknown[]>([]);

  React.useEffect(() => {
    const sp = spfi().using(SPFx(context));
    sp.web.lists.getByTitle('MyList').items()
      .then(setItems)
      .catch(console.error);
  }, [context]);

  return (
    <div>
      <Text size={500} weight="semibold">{title}</Text>
      {/* コンテンツ */}
    </div>
  );
};

export default MyWebPart;
```

## Application Customizer（ヘッダー/フッター注入）

```typescript
import { override } from '@microsoft/decorators';
import { BaseApplicationCustomizer, PlaceholderName } from '@microsoft/sp-application-base';

export default class MyCustomizer extends BaseApplicationCustomizer {
  @override
  public onInit(): Promise<void> {
    const header = this.context.placeholderProvider.tryCreateContent(PlaceholderName.Top);
    if (header) {
      header.domElement.innerHTML = `<div class="custom-header">カスタムヘッダー</div>`;
    }
    return Promise.resolve();
  }
}
```

## プロパティパネル

```typescript
protected getPropertyPaneConfiguration(): IPropertyPaneConfiguration {
  return {
    pages: [{
      header: { description: '設定' },
      groups: [{
        groupName: '基本設定',
        groupFields: [
          PropertyPaneTextField('title', { label: 'タイトル' }),
          PropertyPaneDropdown('listName', {
            label: 'リスト名',
            options: this.listOptions
          })
        ]
      }]
    }]
  };
}
```

## ベストプラクティス

- Fluent UI v9（`@fluentui/react-components`）を使用（v8 は非推奨）
- PnPjs v3 でデータ取得（REST 直書きより型安全）
- `context.pageContext` からテナント情報を取得（ハードコード禁止）
- すべての非同期処理に try/catch を入れる

## 更新履歴
- v1.0: 初版作成
