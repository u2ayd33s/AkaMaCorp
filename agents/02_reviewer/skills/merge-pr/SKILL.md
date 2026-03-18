---
name: merge-pr
description: 承認済みのプルリクエストをマージするスキル。"マージして", "PRをマージ", "承認されたPRを取り込んで", "mainに反映して" などが出たら使用する。必ず review-pr スキルで承認済みであることを確認してからマージする。マージ後はブランチの削除とローカルリポジトリの同期も行う。
---

# PR マージ

承認済みのプルリクエストを main ブランチにマージします。

## マージ前の必須確認

マージを実行する前に以下をすべて確認する:

- [ ] `review-pr` スキルでレビューが完了しているか
- [ ] PR が「承認（approved）」状態になっているか
- [ ] CI / チェックが存在する場合、すべてパスしているか
- [ ] ユーザーへの報告が必要な事項がないか（あれば先に報告する）

```bash
# PR の状態を確認
gh pr view <PR番号> --repo <owner>/<repo> --json state,reviews,statusCheckRollup
```

## マージ手順

### 1. マージ実行

```bash
# squash merge（コミット履歴をまとめる・推奨）
gh pr merge <PR番号> --repo <owner>/<repo> --squash --delete-branch

# merge commit（コミット履歴をそのまま残す）
gh pr merge <PR番号> --repo <owner>/<repo> --merge --delete-branch
```

**マージ方式の選び方**:
| 状況 | 推奨方式 |
|------|---------|
| SKILL.md・ドキュメントのみの変更 | squash |
| 複数の意味のあるコミットを残したい | merge commit |
| 1コミットのみの変更 | squash |

### 2. ローカルリポジトリの同期

```bash
cd <リポジトリパス>
git checkout main
git pull origin main
```

**リポジトリパス早見表**:
| リポジトリ | ローカルパス |
|-----------|------------|
| AkaMaCorp | `C:\Users\u2ayd\OneDrive - 業務ハックLab\ドキュメント - AkamaFolder\MyCorp\AkaMaCorp` |
| sharepoint-scriptloader | `C:\Users\u2ayd\OneDrive - 業務ハックLab\ドキュメント - AkamaFolder\MyCorp\sharepoint-scriptloader` |

### 3. マージ完了の報告

マージ完了後、以下のフォーマットでユーザーに報告する:

```
## マージ完了

**リポジトリ**: <owner>/<repo>
**PR**: #<番号> — <タイトル>
**マージ方式**: squash / merge commit
**マージ先**: main
**ブランチ削除**: 済み

ローカルリポジトリを main の最新状態に同期しました。
```

## マージできない場合

以下の場合はマージせずにユーザーに報告する:

```
## マージ保留 — 報告

**リポジトリ**: <owner>/<repo>
**PR**: #<番号> — <タイトル>

### マージできない理由
- [理由]

### 対応のお願い
- [ユーザーに確認・判断してほしいこと]
```

| 状況 | 対応 |
|------|------|
| コンフリクトが発生している | ユーザーに報告・解消を依頼 |
| レビューが未完了 | `review-pr` スキルでレビューを先に実施 |
| CI が失敗している | ユーザーに報告・修正を依頼 |
| 差し戻し（Request Changes）状態 | 修正完了まで待機、ユーザーに状況を報告 |

## 更新履歴
- v1.0: 初版作成
