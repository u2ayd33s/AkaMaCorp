---
date: 2026-05-03
tags: [knowledge, readme]
---

# knowledge/notes — ナレッジ管理ガイド

個人・エージェント共通のナレッジを一元管理するフォルダ。
プロジェクト横断で再利用できる知識を蓄積する。

---

## ファイル一覧（静的）

### SharePoint

| ファイル | date | タグ | 内容 |
|---------|------|------|------|
| [[Graph vs sharepoint rest api]] | 2026-04-24 | 📚reference | Graph API と SharePoint REST API の使い分け |
| [[SharePointページコメントREST-API]] | 2026-04-24 | 📚reference | コメント取得・投稿の REST API 仕様 |
| [[SharePoint-REST-API-フィールド一覧]] | 2026-04-24 | 📚reference | リスト/ライブラリで取得できるフィールド一覧 |
| [[SharePointページ・ニュース一括再公開マニュアル]] | 2026-04-30 | 📖how-to | PnP PowerShell による一括再公開手順 |
| [[SharePoint一括再公開マニュアル]] | 2026-04-30 | 📖how-to | 同上・詳細スクリプト付き完全版（実績：267ページ/6分） |

### Fluent UI / デザイントークン

| ファイル | date | タグ | 内容 |
|---------|------|------|------|
| [[SharePoint-FluentUI-DesignTokens]] | 2026-04-30 | 📚reference | SharePoint Fluent UI v3 デザイントークン一覧 |
| [[SharePoint-FluentUI-CSS変数リファレンス]] | 2026-04-30 | 📚reference | CSS変数の完全リファレンス |

### デザイン

| ファイル | date | タグ | 内容 |
|---------|------|------|------|
| [[AI生成デザインの「没個性化」問題と脱却ガイド]] | 2026-04-24 | 💡insight | AI デザインの画一化を避けるためのガイド |

---

## ファイル構成

```
knowledge/notes/
  README.md          ← このファイル
  _template.md       ← 新規作成時のテンプレート
  YYYY-MM-DD_タイトル.md  ← ナレッジ本体
```

---

## ナレッジの作り方

### 1. `_template.md` をコピーして新規ファイルを作成

ファイル名: `YYYY-MM-DD_タイトル.md`
例: `2026-04-24_SharePointで403が返る原因.md`

### 2. フロントマターのタグを設定

```yaml
---
date: 2026-04-24
tags:
  - agent/sharepoint   # 誰向けか
  - type/🐛error       # 何の知識か
  - project/chofu      # 関連PJ（任意）
---
```

### 3. 本文を書く

結論・手順・コード例を簡潔に。長くなったら分割する。

---

## タグ一覧

### agent/ — 誰向けのナレッジか

| タグ | 対象エージェント |
|------|----------------|
| `agent/all` | 全員共通 |
| `agent/secretary` | 01 オーケストレーター |
| `agent/reviewer` | 02 PRレビュー |
| `agent/obsidian` | 03 Obsidian管理 |
| `agent/designer` | 04 Web UI/UX・Figma |
| `agent/sharepoint` | 05 SharePoint |
| `agent/frontend` | 06 HTML/CSS/JS |
| `agent/discrepancy-check` | 07 差分チェック |

### type/ — 何の知識か

| タグ | 内容 |
|------|------|
| `type/📖how-to` | やり方・手順 |
| `type/🐛error` | エラー・ハマりポイント |
| `type/🎯decision` | 決定事項・方針 |
| `type/📚reference` | 仕様・リファレンス |
| `type/💡insight` | 気づき・パターン |

### project/ — どのプロジェクトに関係するか（任意）

| タグ | プロジェクト |
|------|------------|
| `project/chofu` | 調布駅バスサイネージ |
| `project/toyota` | 豊田市サイネージ |
| `project/sharepoint` | SharePoint |
| `project/designer` | デザイン Web UI/UX・Figma |

---

## エージェントへの参照指示

作業時に必要なナレッジを渡すには、以下のように指示する：

```
「knowledge/notes/ の中で agent/sharepoint タグのものを読んでから作業して」
```

または特定ファイルを直接指定：

```
「knowledge/notes/2026-04-24_SharePointで403が返る原因.md を参考にして」
```

---

## ナレッジ更新のタイミング

- 作業中に新しいエラーや解決策を発見したとき
- 方針・仕様が変わったとき
- 同じ質問を2回以上したとき（→ ナレッジ化のサイン）

---

## Dataview クエリ例（Obsidian で使用）

### 全ナレッジ一覧

```dataview
TABLE date, tags
FROM "knowledge/notes"
WHERE file.name != "README" AND file.name != "_template"
SORT date DESC
```

### エージェント別

```dataview
TABLE date, file.link AS "タイトル"
FROM "knowledge/notes"
WHERE contains(tags, "agent/sharepoint")
SORT date DESC
```

### タイプ別（エラーのみ）

```dataview
TABLE date, file.link AS "タイトル"
FROM "knowledge/notes"
WHERE contains(tags, "type/🐛error")
SORT date DESC
```
