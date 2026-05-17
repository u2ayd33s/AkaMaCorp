# SPFx カスタム HTML Web パーツ 実装ガイド

> SharePoint Framework（SPFx）で HTML / CSS / JS を自由に書き込める Web パーツを作る手順をまとめたドキュメントです。

---

## 目次

1. [環境構築](#1-環境構築)
2. [プロジェクト生成](#2-プロジェクト生成)
3. [基本実装（render メソッド）](#3-基本実装render-メソッド)
4. [リアルタイムプレビュー](#4-リアルタイムプレビュー)
5. [コードエディタ強化（Monaco Editor）](#5-コードエディタ強化monaco-editor)
6. [プロパティパネル設定](#6-プロパティパネル設定)
7. [UI・見た目のブラッシュアップ](#7-ui見た目のブラッシュアップ)
8. [セキュリティ強化](#8-セキュリティ強化)
9. [ビルド・デプロイ](#9-ビルドデプロイ)
10. [完成イメージ](#10-完成イメージ)

---

## 1. 環境構築

### 必要なツール

| ツール | バージョン |
|--------|-----------|
| Node.js | v18系（推奨） |
| Yeoman | 最新 |
| SPFx Generator | 最新 |
| gulp-cli | 最新 |

### インストール

```bash
npm install -g @microsoft/generator-sharepoint yo gulp-cli
```

---

## 2. プロジェクト生成

```bash
mkdir my-webpart && cd my-webpart
yo @microsoft/sharepoint
```

### 対話形式の選択肢

| 質問 | 選択内容 |
|------|----------|
| Which type of client-side component? | **WebPart** |
| Web part name | 任意（例：MyHtmlPart） |
| Which template? | **No framework** ← 重要！ |

### 生成されるフォルダ構成

```
src/
└── webparts/
    └── myHtmlPart/
        ├── MyHtmlPartWebPart.ts        ← メインファイル
        └── MyHtmlPartWebPart.module.scss  ← CSS
```

---

## 3. 基本実装（render メソッド）

`MyHtmlPartWebPart.ts` の `render()` が核心です。

```typescript
public render(): void {
  const savedHtml = this.properties.customHtml || '';

  this.domElement.innerHTML = `
    <div>
      ${this.displayMode === DisplayMode.Edit ? `
        <textarea id="htmlInput" style="
          width: 100%; height: 150px;
          font-family: monospace; font-size: 13px;
          padding: 8px; box-sizing: border-box;
          border: 2px solid #0078d4; border-radius: 4px;
        ">${savedHtml}</textarea>
        <button id="applyBtn">💾 適用（保存）</button>
        <hr/>
      ` : ''}
      <iframe id="preview"
        style="width:100%; border:none; min-height:200px;"
        sandbox="allow-scripts">
      </iframe>
    </div>
  `;

  // iframe に HTML を流し込む
  const applyHtml = (html: string) => {
    const iframe = this.domElement.querySelector('#preview') as HTMLIFrameElement;
    const doc = iframe.contentDocument || iframe.contentWindow?.document;
    if (doc) { doc.open(); doc.write(html); doc.close(); }
  };

  if (savedHtml) applyHtml(savedHtml);

  if (this.displayMode === DisplayMode.Edit) {
    const btn = this.domElement.querySelector('#applyBtn');
    const textarea = this.domElement.querySelector('#htmlInput') as HTMLTextAreaElement;

    btn?.addEventListener('click', () => {
      this.properties.customHtml = textarea.value; // 保存
      applyHtml(textarea.value);
    });
  }
}
```

### プロパティの型定義

```typescript
// IMyHtmlPartWebPartProps.ts
export interface IMyHtmlPartWebPartProps {
  customHtml: string;
  title: string;
  previewHeight: number;
  allowScripts: boolean;
  showEditor: boolean;
}
```

---

## 4. リアルタイムプレビュー

`input` イベントを追加するだけで、入力するたびに即時反映されます。

```typescript
if (this.displayMode === DisplayMode.Edit) {
  const btn      = this.domElement.querySelector('#applyBtn');
  const textarea = this.domElement.querySelector('#htmlInput') as HTMLTextAreaElement;
  const savedMsg = this.domElement.querySelector('#savedMsg') as HTMLElement;

  // ✅ リアルタイムプレビュー（入力のたびに反映）
  textarea?.addEventListener('input', () => {
    applyHtml(textarea.value);
  });

  // 💾 適用ボタンは「保存」専用
  btn?.addEventListener('click', () => {
    this.properties.customHtml = textarea.value;
    applyHtml(textarea.value);

    // 完了メッセージを2秒表示
    savedMsg.style.display = 'inline';
    setTimeout(() => { savedMsg.style.display = 'none'; }, 2000);
  });
}
```

### 動作フロー

```
textarea に入力
    ↓（input イベント）
プレビューに即反映  ←── 確認できる
    ↓
[ 適用 ] クリック
    ↓
SharePoint に保存される
```

---

## 5. コードエディタ強化（Monaco Editor）

VS Code と同じエディタを埋め込みます。

### インストール

```bash
npm install @monaco-editor/react monaco-editor
```

### iframe 内に Monaco を読み込む（CDN 利用）

```typescript
const editorHtml = `
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/monaco-editor@0.44.0/min/vs/loader.js"></script>
</head>
<body style="margin:0">
  <div id="editor" style="height:100vh"></div>
  <script>
    require.config({
      paths: { vs: 'https://cdn.jsdelivr.net/npm/monaco-editor@0.44.0/min/vs' }
    });
    require(['vs/editor/editor.main'], function () {
      const editor = monaco.editor.create(document.getElementById('editor'), {
        value: ${JSON.stringify(savedHtml)},
        language: 'html',
        theme: 'vs-dark',           // ダークテーマ
        fontSize: 13,
        minimap: { enabled: false },
        wordWrap: 'on',
        automaticLayout: true
      });

      // 入力のたびに親フレームへ送信
      editor.onDidChangeModelContent(() => {
        window.parent.postMessage({
          type: 'codeChange',
          value: editor.getValue()
        }, '*');
      });
    });
  </script>
</body>
</html>
`;
```

### 親側でメッセージを受信

```typescript
let currentHtml = savedHtml;

window.addEventListener('message', (event) => {
  if (event.data?.type === 'codeChange') {
    applyHtml(event.data.value);
    currentHtml = event.data.value; // 一時保存
  }
});
```

---

## 6. プロパティパネル設定

編集画面右側のパネルに設定項目を追加します。

```typescript
protected getPropertyPaneConfiguration(): IPropertyPaneConfiguration {
  return {
    pages: [
      {
        header: { description: 'Web パーツの設定' },
        groups: [
          {
            groupName: '表示設定',
            groupFields: [
              PropertyPaneTextField('title', {
                label: 'タイトル（任意）',
                placeholder: '例：お知らせエリア'
              }),
              PropertyPaneSlider('previewHeight', {
                label: 'プレビューの高さ（px）',
                min: 100, max: 800, step: 50, value: 300
              }),
              PropertyPaneToggle('showEditor', {
                label: '編集エリアを常に表示',
                onText: 'ON', offText: 'OFF'
              })
            ]
          },
          {
            groupName: 'セキュリティ設定',
            groupFields: [
              PropertyPaneToggle('allowScripts', {
                label: 'JavaScript を許可する',
                onText: '許可', offText: '禁止'
              })
            ]
          }
        ]
      }
    ]
  };
}
```

### 設定項目一覧

| 項目 | 種類 | 説明 |
|------|------|------|
| タイトル | TextField | Web パーツのヘッダーに表示 |
| プレビューの高さ | Slider | 100〜800px で調整 |
| 編集エリア常時表示 | Toggle | 閲覧モードでも textarea を表示 |
| JavaScript を許可 | Toggle | 他ユーザーへの JS 実行権限 |

---

## 7. UI・見た目のブラッシュアップ

左右2カラムレイアウトで、入力とプレビューを並べて表示します。

```typescript
this.domElement.innerHTML = `
<div style="font-family:'Segoe UI',sans-serif; border:1px solid #e1e1e1;
     border-radius:8px; overflow:hidden; box-shadow:0 2px 8px rgba(0,0,0,0.1);">

  <!-- ヘッダーバー -->
  <div style="background:#0078d4; color:white; padding:10px 16px;
       display:flex; align-items:center; justify-content:space-between;">
    <span style="font-weight:600; font-size:15px;">
      📄 ${this.properties.title || 'HTML エディタ'}
    </span>
    <div style="display:flex; gap:8px;">
      <button id="clearBtn" style="background:rgba(255,255,255,0.2); color:white;
        border:none; border-radius:4px; padding:4px 10px; cursor:pointer; font-size:12px;">
        🗑 クリア
      </button>
    </div>
  </div>

  <!-- 左右レイアウト -->
  <div style="display:flex; height:${this.properties.previewHeight || 300}px;">

    <!-- 左：エディタ -->
    <div style="flex:1; border-right:1px solid #e1e1e1;">
      <div style="background:#f8f8f8; padding:4px 12px;
           font-size:11px; color:#666; border-bottom:1px solid #e1e1e1;">
        📝 コード入力
      </div>
      <iframe id="editorFrame" style="width:100%; height:calc(100% - 28px);
        border:none;" sandbox="allow-scripts allow-same-origin"></iframe>
    </div>

    <!-- 右：プレビュー -->
    <div style="flex:1;">
      <div style="background:#f8f8f8; padding:4px 12px;
           font-size:11px; color:#666; border-bottom:1px solid #e1e1e1;">
        👁 リアルタイムプレビュー
      </div>
      <iframe id="preview" style="width:100%; height:calc(100% - 28px);
        border:none; background:white;"
        sandbox="${this.properties.allowScripts ? 'allow-scripts' : ''}">
      </iframe>
    </div>

  </div>

  <!-- フッター -->
  <div style="padding:8px 16px; background:#f8f8f8; border-top:1px solid #e1e1e1;
       display:flex; align-items:center; justify-content:space-between;">
    <span id="statusMsg" style="font-size:12px; color:#666;">準備完了</span>
    <div style="display:flex; align-items:center; gap:12px;">
      <span id="savedMsg" style="color:green; font-size:13px; display:none;">
        ✅ 保存しました！
      </span>
      <button id="applyBtn" style="padding:7px 20px; background:#0078d4; color:white;
        border:none; border-radius:4px; cursor:pointer; font-size:13px; font-weight:600;">
        💾 適用（保存）
      </button>
    </div>
  </div>

</div>
`;
```

---

## 8. セキュリティ強化

他ユーザーに使わせる場合の重要設定です。

### 危険なタグを除去するサニタイズ関数

```typescript
private sanitizeHtml(html: string): string {
  if (!this.properties.allowScripts) {
    // script タグを除去
    html = html.replace(/<script[\s\S]*?<\/script>/gi, '<!-- script 無効 -->');
    // インラインイベントを除去（onclick 等）
    html = html.replace(/on\w+="[^"]*"/gi, '');
    html = html.replace(/on\w+='[^']*'/gi, '');
  }
  return html;
}
```

### iframe の sandbox 属性

```typescript
// allowScripts が ON のとき JS 実行を許可
sandbox="${this.properties.allowScripts ? 'allow-scripts' : ''}"

// ⚠️ allow-same-origin は原則禁止
// → SharePoint の認証情報にアクセスできてしまう
```

### CSP（コンテンツセキュリティポリシー）の追加

```typescript
const secureHtml = `
  <meta http-equiv="Content-Security-Policy"
    content="default-src 'self' 'unsafe-inline';
             img-src *;
             script-src 'unsafe-inline';">
  ${this.sanitizeHtml(html)}
`;
```

### セキュリティ設定の使い分け

| 利用シーン | 推奨設定 |
|------------|----------|
| 管理者・開発者のみ | `allowScripts: true` でもOK |
| 一般ユーザーも使う | `allowScripts: false` を推奨 |
| 外部データを表示する | CSP を必ず追加 |

---

## 9. ビルド・デプロイ

```bash
# ローカルでテスト
gulp serve
# → https://localhost:4321/temp/workbench.html を開く

# 本番用ビルド
gulp bundle --ship

# .sppkg ファイルを生成
gulp package-solution --ship
```

### デプロイ手順

1. `sharepoint/solution/` 内の `.sppkg` ファイルを確認
2. SharePoint 管理センター → **アプリカタログ** を開く
3. `.sppkg` ファイルをアップロード
4. 「このソリューションを信頼する」をクリック
5. サイトに Web パーツを追加して完了！

---

## 10. 完成イメージ

```
┌────────────────────────────────────────────────────┐
│ 📄 HTML エディタ                        [🗑 クリア] │  ← ヘッダー
├─────────────────────┬──────────────────────────────┤
│ 📝 コード入力        │ 👁 リアルタイムプレビュー      │
│ ┌─────────────────┐ │ ┌────────────────────────┐  │
│ │ Monaco Editor   │ │ │                        │  │
│ │ (VSCode 風)     │→│ │  Hello!（赤色で表示）   │  │
│ │                 │ │ │                        │  │
│ └─────────────────┘ │ └────────────────────────┘  │
├─────────────────────┴──────────────────────────────┤
│ 準備完了               ✅ 保存しました！[💾 適用]   │  ← フッター
└────────────────────────────────────────────────────┘

プロパティパネル（右サイドバー）
├── 📝 タイトル入力
├── 📏 プレビューの高さ（スライダー）
├── 👁 編集エリア常時表示（トグル）
└── 🔒 JavaScript を許可（トグル）
```

### 編集モード / 閲覧モードの違い

| | 編集モード | 閲覧モード |
|---|---|---|
| エディタ | 表示 | 非表示 |
| プレビュー | 表示 | 表示 |
| 適用ボタン | 表示 | 非表示 |

---

## まとめ

```
環境構築
  ↓
yo で No framework プロジェクト生成
  ↓
render() に HTML 書き込み ＋ iframe でプレビュー
  ↓
input イベントでリアルタイムプレビュー追加
  ↓
Monaco Editor でコードエディタ強化
  ↓
プロパティパネルで管理者向け設定追加
  ↓
sanitizeHtml + sandbox でセキュリティ強化
  ↓
gulp build → .sppkg → アプリカタログへデプロイ
```

> **ポイント：** 他ユーザーに使わせる場合は `allowScripts` トグルで管理者が JS の実行可否を制御するのが安全です。
