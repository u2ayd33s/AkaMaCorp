---
name: review-pr
description: 各エージェントのプルリクエストを確認・検証するスキル。"PRをレビューして", "プルリクを確認して", "変更をチェックして", "マージしていい？", "PRに問題ない？" などが出たら積極的に使用する。エージェントごとのレビュー基準に従い、承認・差し戻し・ユーザー報告の判断を行う。
---

# PR レビュー

プルリクエストの内容を確認・検証し、承認・差し戻し・ユーザー報告を判断します。

## レビュー手順

### 1. PR 情報の取得

```bash
# PR 一覧を確認
gh pr list --repo <owner>/<repo>

# PR の詳細・差分を確認
gh pr view <PR番号> --repo <owner>/<repo>
gh pr diff <PR番号> --repo <owner>/<repo>

# PR のファイル変更一覧
gh pr view <PR番号> --repo <owner>/<repo> --json files
```

### 2. エージェント別レビュー基準でチェック

対象リポジトリ・ブランチ名から担当エージェントを判断し、対応する基準を適用する。

---

## エージェント別レビュー基準

### sharepoint エージェントのPR（AkaMaCorp）

**SKILL.md の変更**
- [ ] frontmatter（name・description）が正しく記述されているか
- [ ] description にトリガーキーワードが含まれているか
- [ ] コードサンプルに構文エラーがないか
- [ ] 更新履歴が追記されているか

**agent-registry.md の変更**
- [ ] 新スキル追加時にレジストリへの追記があるか

---

### sharepoint エージェントのPR（sharepoint-scriptloader）

**JS ファイルの変更**
- [ ] `window.registerSpLoaderFire()` で部分更新に対応しているか（即時実行でない処理）
- [ ] `window.onload` / `addEventListener('load')` を使う場合、`document.readyState` チェックがあるか
- [ ] グローバルスコープを汚染する変数宣言がないか（即時実行関数でラップされているか）
- [ ] エラーハンドリング（try/catch）が含まれているか（API呼び出しがある場合）
- [ ] ファイル名が命名規則に従っているか（`__` の使い方・対象スコープが正しいか）

**CSS ファイルの変更**
- [ ] スコープを絞ったセレクタになっているか（全体に影響する広いセレクタがないか）
- [ ] SharePoint 標準 UI を壊す可能性のあるスタイル上書きがないか
- [ ] ファイル名が命名規則に従っているか

---

### frontend エージェントのPR（AkaMaCorp）

**SKILL.md の変更**
- [ ] frontmatter が正しく記述されているか
- [ ] HTML サンプルに ARIA 属性など基本的なアクセシビリティ対応があるか
- [ ] CSS サンプルにスコープが設定されているか
- [ ] 更新履歴が追記されているか

---

### secretary / reviewer エージェントのPR（AkaMaCorp）

**CLAUDE.md・SKILL.md の変更**
- [ ] 役割・スキルの記述に矛盾がないか
- [ ] agent-registry.md に反映されているか（新エージェント追加の場合）
- [ ] README.md のエージェント構成表が更新されているか

---

## レビュー結果の判断

### 承認してよい場合
すべてのチェックがパスした場合。`merge-pr` スキルに引き継ぐ。

```bash
gh pr review <PR番号> --repo <owner>/<repo> --approve --body "レビュー完了。基準をすべて満たしています。"
```

### 差し戻す場合
1つ以上のチェックが失敗した場合。コメントで具体的な修正箇所を指摘する。

```bash
gh pr review <PR番号> --repo <owner>/<repo> --request-changes --body "以下の修正をお願いします。\n\n- [指摘内容]"
```

### ユーザーに報告する場合

以下のフォーマットで報告する:

```
## PR レビュー報告

**リポジトリ**: <owner>/<repo>
**PR**: #<番号> — <タイトル>
**ブランチ**: <ブランチ名>

### 確認できた問題 / 疑問点
- [内容]

### 判断をお願いしたいこと
- [具体的な質問・確認事項]
```

## 更新履歴
- v1.0: 初版作成
