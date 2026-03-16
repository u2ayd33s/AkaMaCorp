# エージェントレジストリ

秘書エージェントが管理する全エージェントと、そのスキル一覧。
新しいエージェントを追加したらここを必ず更新してください。

---

## 登録エージェント

### sharepoint
- **パス**: `agents/sharepoint/`
- **専門領域**: SharePoint Online / SPFx / REST API / Microsoft Graph
- **得意なこと**: SPFx Web パーツ・拡張機能の開発、SharePoint REST API 実装、PnPjs 活用
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `spfx-component` | SPFx Web パーツ・拡張機能の雛形生成と実装 |
  | `sp-rest-api` | SharePoint REST API / PnPjs コード生成 |
  | `xsp-script-loader` | ページパスに対応した JS/CSS 自動注入によるカスタマイズ開発 |
- **向いているタスク例**:
  - SharePoint リストのデータ取得・更新処理を作りたい
  - SPFx で社内ポータルのコンポーネントを開発したい
  - Graph API でユーザー情報を取得したい
  - SharePoint の特定ページにカスタムデザインや機能を追加したい
  - SPFx を使わずにページをカスタマイズしたい（ScriptLoader 経由）

---

### frontend
- **パス**: `agents/frontend/`
- **専門領域**: HTML / CSS / JavaScript / UI/UX / レスポンシブデザイン
- **得意なこと**: UI コンポーネント生成、CSS レイアウト設計、アクセシビリティ対応
- **スキル**:
  | スキル | 概要 |
  |--------|------|
  | `ui-component` | モーダル・タブ・フォームなど再利用可能コンポーネント生成 |
  | `css-layout` | Grid/Flex を使ったレスポンシブレイアウト設計 |
- **向いているタスク例**:
  - ページのレイアウトを作りたい
  - モーダルやドロップダウンなど UI パーツが欲しい
  - モバイル対応のデザインを実装したい

---

## エージェント追加手順

1. `agents/<エージェント名>/` ディレクトリを作成
2. `CLAUDE.md` にエージェントの役割・スキルを定義
3. `skills/<スキル名>/SKILL.md` を作成
4. このファイル (`agent-registry.md`) に追記
5. `README.md` のエージェント構成表を更新
