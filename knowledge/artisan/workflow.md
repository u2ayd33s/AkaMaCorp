---
tags: [knowledge, artisan, workflow]
updated: 2026-08-31
---

# artisan 開発ワークフロー

## 環境

| 項目 | 値 |
|------|-----|
| ローカル環境 | `D:\2026artisan\` |
| テーマパス | `app\public\wp-content\themes\www.artisan.jp.net` |
| GitHub | `u2ayd33s/artisan` |

## ブランチ戦略

```
main
└── feat/<feature-name>   # 機能開発
└── fix/<bug-name>        # バグ修正
└── chore/<task-name>     # 設定・メンテ
```

## 開発フロー

1. `main` からブランチ作成
2. 実装・テスト（ローカル WordPress で確認）
3. `git add <file>` で個別ステージング（`git add -A` 禁止）
4. `git diff --cached` で内容確認
5. コミット → `u2ayd33s/artisan` に PR

## コミットメッセージ規約

```
feat: 新機能の概要
fix: バグ修正の概要
chore: 設定・メンテの概要
refactor: リファクタリングの概要
style: CSS/デザイン変更の概要
```

## よく使うコマンド

```bash
cd "D:/2026artisan/app/public/wp-content/themes/www.artisan.jp.net"
git status
git log --oneline -10
```
