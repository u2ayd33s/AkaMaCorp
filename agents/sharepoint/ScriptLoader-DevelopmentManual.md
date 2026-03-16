# スクリプトローダー開発マニュアル

## 動作

- ScriptLoaderは、「サイトのリソース ファイル」ライブラリの「ScriptLoader」フォルダ（サイトのURL/SiteAssets/ScriptLoader）配下にある「.js」、「.css」ファイルを読み込み、サイトのページを表示した際に読み込まれます。

## ファイル名規則

- SiteAssets/ScriptLoaderに配置する「.js」、「.css」ファイル名は、以下の通り命名してください。
  - **global.js / global.css（名称固定）**
    - サイト全体が対象で、どのページでも読み込まれます。

  - **SitePages__.js / SitePages__.css**
  ※ SitePages以外のページライブラリが存在する場合には、利用するページライブラリのパス名のファイルを作成してください。（SitePages2__.jsなど）
    - パス名のファイルは、拡張子の前にアンダースコアを2つ記述してください。
    - 「サイトのページ」ライブラリ（〜/SitePages/）配下のすべてのページで読み込まれます。※ global.js, global.cssも読み込まれます。
    SitePages配下にフォルダがあり、そのフォルダ配下にあるすべてのページで読み込ませる場合には、以下のようなファイル名にしてください。
      - SitePages/Category1フォルダの配下にあるすべてのページで読み込ませたい場合、SitePages__Category1__.js, SitePages__Category1__.css（アンダースコアは2つ記述してください）
      ※ この場合、SitePages/Category2が存在しても、SitePages/Category2フォルダの配下にあるページでは読み込みはおこなわれません。

  - **SitePages__{ファイル名}.js / SitePages__{ファイル名}.css**（アンダースコアは2つ記述してください）
  ※ ページがフォルダ配下に存在する場合、フォルダ名も含めてください。（〜/SitePages/sample/test.aspxの場合 : SitePages__sample__test.js, SitePages__sample__test.css）
    - {ファイル名}に指定した特定のページ（〜/SitePages/{ファイル名}.aspx）で読み込まれます。※ global.js、SitePages.jsも読み込まれます。

## ファイル内の記述ルール

### 「.js」ファイル

- 関数内でなく、グローバルに記述されたコードは、読み込み時に処理がおこなわれます。
- SharePointが部分更新でページ遷移した場合にも処理をおこないたいときは、以下のように window.registerSpLoaderFire() の引数に実施したい関数を指定してください。
  ※ window.registerSpLoaderFire() に指定した関数以外の関数などを記述した場合は、読み込みはおこなわれますが、部分更新のページ遷移の際は処理がおこなわれません。

  ```
  (() => {
    // イベントリスナーの登録処理（引数に登録したい関数名を記述）
    window.registerSpLoaderFire(loaderFire);

    // イベントリスナーに登録したい関数を記述
    function loaderFire(event)
    {
      // 実施したい処理を記述
    }
  })();
  ```

#### ※「.js」ファイルを記述する際の注意点

- window.onload や window.addEventListener('load', onLoadHandler) のように、ロードイベントを監視する処理を記述する場合、「.js」ファイルが実行されるタイミングによってはロードが完了している場合があります。
document.readyState でロードが完了しているかを確認する処理を入れてください。

  ```
  例）
  if (document.readyState === 'complete') {
    // ページの読み込みが完了している場合の処理
    onLoadHandler();
  } else {
    // ページの読み込みが完了していない場合の処理
    window.addEventListener('load', onLoadHandler)
  }
  ```

### .css

- 一般的なCSSの記述方法で記述します。


## ファイルアップロード手順

1. アップロードするSharePointサイトを開きます。
1. 歯車から「サイトコンテンツ」をクリックします。
1. 「サイトのリソースファイル」をクリックします。
1. 「ScriptLoader」をクリックします。
1. .js, .cssファイルをアップロードします。

## 動作確認

1. ～/SiteAssets/ScriptLoader内に作成したファイルをアップロードします。
1. 確認対象のページを開き、記述したスクリプトやスタイルシートが動作していることを確認してください。
1. global.js, global.cssは、複数のページで動作していることを確認してください。
1. SitePages.js, SitePages.css, のようにライブラリを対象にしたものや、SitePages__Category1.js, SitePages__Category1.css のようにフォルダを対象にしたスクリプトやスタイルシートが、ライブラリやフォルダ配下の複数のページで動作していることを確認してください。

**※ .jsファイルを修正後、反映されていないと思われる場合は、ブラウザを強制リフレッシュしてみてください。**

### 開発者ツールを使ったデバッグ実行

1. デバッグしたいサイトのページを開きます。
1. F12キーを押し、開発者ツールを開きます。
1. ソースタブをクリックします。
1. 左側のツリーより、SiteAssetsのScriptLoaderを展開します。
1. デバッグするスクリプトファイルを開き、ブレークポイントを設定して確認してください。

※ScriptLoaderでファイルを読み込む際、スクリプトのURLにクエリー文字列「v=〜」でタイムスタンプを設定し、定期的にブラウザに再読み込みさせています。
タイムスタンプは10分単位で更新しています。
タイムスタンプが変わるまで、ブラウザはキャッシュしたファイルを使用するので、サーバー側で変更されたファイルは基本的には読み込まれないためご注意ください。
例）
以下の例では 9:40 以降にファイルを更新した場合、9:50 までタイムスタンプが変わらないため、更新後のファイルはブラウザに読み込まれません。

2025/4/10 9:40:00 から 9:49:59 までの読み込み
→ クエリー文字列のタイムスタンプは v=202504100940
2025/4/10 9:50:00 以降の読み込み
→ クエリー文字列のタイムスタンプは v=202504100950

### writeDebugInfo ログの確認方法

- writeDebugInfo のログを確認する場合には、ページのURLの末尾にクエリパラメーター `?debug=console` を付与してください。
  開発者ツールのコンソールに出力されるようになります。

### ページコンテキスト・ユーザープロファイルの利用

- スクリプトローダーでは、SharePointのapiを利用して現在のユーザープロファイルを取得し、ページコンテキストと共にカスタムWindowオブジェクトに渡しています。
- 開発する「.js」ファイルでは、取得したユーザープロファイルやページコンテキストを参照することが可能です。
  - オブジェクト名（変数名）
    - ページコンテキスト: window.SPLoader.PageContext
    - ユーザープロファイル: window.SPLoader.UserProperties

```
    例）メールアドレスを取得
    var eMail = window?.SPLoader?.UserProperties?.Email;

    例）サイトURLを取得
    var siteUrl = window?.SPLoader?.PageContext.web.absoluteUrl;
```

※ UserProperties で利用できる項目については、こちらを参照ください。
https://learn.microsoft.com/en-us/previous-versions/office/sharepoint-csom/jj163873(v=office.15)
※ PageContext で利用できる項目については、こちらを参照ください。
https://learn.microsoft.com/ja-jp/javascript/api/sp-page-context/pagecontext?view=sp-typescript-latest
