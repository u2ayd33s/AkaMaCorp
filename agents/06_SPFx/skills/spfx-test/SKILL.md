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

## 更新履歴
- v1.0: 初版作成
