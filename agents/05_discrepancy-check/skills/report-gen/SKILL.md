---
name: report-gen
description: diff-check の結果をMarkdownレポートに変換して reports/ へ出力するスキル。"レポート", "出力", "結果まとめ", "報告書" などのキーワードが出たら使用する。差分ゼロの場合も「異常なし」レポートを必ず出力する。
---

# レポート生成

差分チェック結果をMarkdownレポートとして出力します。

## 出力先

`projects/<project-id>/reports/YYYY-MM-DD_<project-id>_report.md`

例: `projects/chofu-station/reports/2026-03-24_chofu-001_report.md`

## レポートテンプレート

```markdown
# 差分チェックレポート

- **プロジェクト**: chofu-001 — 1番のりば 京王バス 調布駅南口行き
- **チェック日時**: 2026-03-24 09:00
- **Excelデータ件数**: 42件
- **Figmaデータ件数**: 40件
- **差分件数**: 3件

---

## サマリー

| 重大度 | 件数 |
|--------|------|
| 🔴 HIGH | 1 |
| 🟠 MEDIUM | 1 |
| 🟡 LOW | 1 |

---

## 差分詳細

### 🔴 [MISMATCH] destinationEn

- **Excel**: `Shinjuku Station`
- **Figma**: `Shinjuku Sta.`
- **Figmaノード**: `123:456`

> **修正方法**: Figmaデザインの `Destination/En` ノードを `Shinjuku Station` に修正してください。

---

### 🟠 [MISSING_IN_FIGMA] times — 23:30

- **Excel**: `23:30`
- **Figma**: _(未登録)_

> **修正方法**: Figmaデザインに時刻 `23:30` のノードを追加してください。

---

## 結論

差分が **3件** 検出されました。上記の修正を完了後、再チェックを実施してください。
```

## 差分ゼロの場合

```markdown
## 結論

差分は **0件** です。ExcelデータとFigmaデザインは一致しています。✅
```

## ルール

- 重大度の高い順（HIGH → MEDIUM → LOW）で並べる
- 各差分に修正方法のガイドを必ず付記する
- レポート生成後、`figma/urls.json` の `lastChecked` を現在日時に更新する
- ユーザーへの報告は「差分件数 + HIGH件数」を先頭に表示する
