---
name: ui-component
description: 再利用可能な UI コンポーネントを HTML/CSS/JavaScript で生成するスキル。"コンポーネント作って", "ボタン", "モーダル", "カード", "フォーム", "ナビゲーション", "タブ", "アコーディオン", "トースト通知" などの UI 要素が登場したら積極的に使用する。アクセシビリティ (ARIA) 対応・再利用性・保守性を重視した実装を生成する。
---

# UI コンポーネント生成

再利用性・アクセシビリティを重視した UI コンポーネントを生成します。

## 設計原則

1. **再利用性**: クラス名・データ属性でカスタマイズ可能にする
2. **アクセシビリティ**: ARIA 属性・キーボード操作を標準で実装
3. **依存ゼロ**: ライブラリ不要、バニラ JS で動作
4. **BEM 命名**: CSS クラスは BEM 規則に従う

## コンポーネントカタログ

### モーダル
```html
<div class="modal" id="myModal" role="dialog" aria-modal="true" aria-labelledby="modalTitle" hidden>
  <div class="modal__overlay" data-modal-close></div>
  <div class="modal__container">
    <header class="modal__header">
      <h2 class="modal__title" id="modalTitle">タイトル</h2>
      <button class="modal__close" aria-label="閉じる" data-modal-close>×</button>
    </header>
    <div class="modal__body">
      <!-- コンテンツ -->
    </div>
    <footer class="modal__footer">
      <button class="btn btn--secondary" data-modal-close>キャンセル</button>
      <button class="btn btn--primary" id="modalConfirm">確認</button>
    </footer>
  </div>
</div>
```

```javascript
class Modal {
  constructor(id) {
    this.el = document.getElementById(id);
    this.el.querySelectorAll('[data-modal-close]').forEach(el => {
      el.addEventListener('click', () => this.close());
    });
    document.addEventListener('keydown', e => {
      if (e.key === 'Escape') this.close();
    });
  }
  open() {
    this.el.removeAttribute('hidden');
    this.el.querySelector('.modal__container').focus();
    document.body.style.overflow = 'hidden';
  }
  close() {
    this.el.setAttribute('hidden', '');
    document.body.style.overflow = '';
  }
}
```

### トースト通知
```javascript
function showToast(message, type = 'info', duration = 3000) {
  const toast = document.createElement('div');
  toast.className = `toast toast--${type}`;
  toast.setAttribute('role', 'alert');
  toast.textContent = message;
  document.body.appendChild(toast);
  requestAnimationFrame(() => toast.classList.add('toast--visible'));
  setTimeout(() => {
    toast.classList.remove('toast--visible');
    toast.addEventListener('transitionend', () => toast.remove());
  }, duration);
}
```

### タブ
```html
<div class="tabs" data-tabs>
  <div class="tabs__list" role="tablist">
    <button class="tabs__tab tabs__tab--active" role="tab" aria-selected="true" aria-controls="panel1">タブ 1</button>
    <button class="tabs__tab" role="tab" aria-selected="false" aria-controls="panel2">タブ 2</button>
  </div>
  <div class="tabs__panel" id="panel1" role="tabpanel">コンテンツ 1</div>
  <div class="tabs__panel" id="panel2" role="tabpanel" hidden>コンテンツ 2</div>
</div>
```

## 作業フロー

1. ユーザーが欲しいコンポーネントを確認する
2. 用途・デザイントーン・制約 (フレームワーク使用可否など) を確認する
3. HTML 構造 → CSS → JS の順で実装する
4. アクセシビリティチェックリストを確認する

## アクセシビリティチェックリスト

- [ ] インタラクティブ要素に適切な `role` 属性
- [ ] フォームラベルの `for` / `aria-label` 設定
- [ ] キーボード操作 (Tab, Enter, Escape, 矢印キー)
- [ ] フォーカス表示 (`:focus-visible` スタイル)
- [ ] カラーコントラスト比 4.5:1 以上 (WCAG AA)

## 更新履歴
- v1.0: 初版作成
