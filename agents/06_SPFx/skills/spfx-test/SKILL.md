---
name: spfx-test
description: SPFx プロジェクト向け Jest ユニットテスト・Playwright E2E テストの設計と実装を支援するスキル。コンポーネントのレンダリングテスト・API モック・E2E シナリオ作成をカバーする。
---

# SPFx テスト

## Jest ユニットテスト（コンポーネント）

```typescript
import * as React from 'react';
import { render, screen } from '@testing-library/react';
import MyWebPart from '../components/MyWebPart';

describe('MyWebPart', () => {
  it('タイトルを表示する', () => {
    render(<MyWebPart title="テスト" context={mockContext} />);
    expect(screen.getByText('テスト')).toBeInTheDocument();
  });
});
```

## PnPjs のモック

```typescript
jest.mock('@pnp/sp', () => ({
  spfi: jest.fn(() => ({
    using: jest.fn().mockReturnThis(),
    web: {
      lists: {
        getByTitle: jest.fn().mockReturnValue({
          items: jest.fn().mockResolvedValue([{ Id: 1, Title: 'テスト' }])
        })
      }
    }
  }))
}));
```

## テスト実行

```bash
npm test              # watch モード
npm test -- --coverage  # カバレッジ計測
```

## 実ページでの動作確認（ローカルデバッグ）

Jest / E2E の前後に、実 SharePoint ページでローカルコードを確認する。手順は [[knowledge/SPFx/spfx-debug-local|spfx-debug-local]]。

```bash
gulp serve --nobrowser   # SPFx 1.21+ は heft start --nobrowser
# ページ URL に ?debug=true&noredir=true&debugManifestsFile=https://localhost:4321/temp/manifests.js
# （1.21+ は /temp/build/manifests.js）
```

- 初回のみ: 管理センター > 詳細設定 > スクリプト ソースに `https://localhost:4321` を追加（CSP。開発テナントのみ）、ブラウザのローカルネットワークアクセスを許可
- `Script error` はコンソールの最初のエラーで切り分ける

## 更新履歴
- v1.0: 初版作成
- v1.1: 実ページでのローカルデバッグ手順を追加（2026-10-28）
