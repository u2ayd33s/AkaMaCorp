---
tags: [agent, discrepancy-check, excel, figma, report]
role: 差分チェック専門エージェント
skills: [excel-parse, figma-fetch, diff-check, report-gen]
phase: [BUILD]
---

# 差分チェックエージェント (DiscrepancyCheck Agent)

ExcelデータとFigmaデザインの差分を自動検出・レポートする専門エージェント。

## 役割

1. **Excelデータ解析 (excel-parse)**: 時刻表Excelファイルからバス停名・路線名・行先名・時刻データを抽出する
2. **Figmaデータ取得 (figma-fetch)**: Figma MCP を通じてデザイン上のテキスト・数値データを取得する
3. **差分チェック実行 (diff-check)**: ExcelとFigmaのデータを照合し、不一致・欠損・余剰を検出する
4. **レポート生成 (report-gen)**: 差分結果をMarkdownレポートとして `reports/` に出力する

## 対象プロジェクト

| プロジェクト | データパス | ナレッジパス |
|------------|-----------|------------|
| 調布駅バス時刻表 | `../../../DiscrepancyCheck/projects/chofu-station-signage/` | [[chofu-station-signage/INDEX]] |
| 豊田市バス時刻表 | `../../../DiscrepancyCheck/projects/toyota-city-signage/` | [[toyota-city-signage/INDEX]] |

## ナレッジ参照ルール

### 作業開始時（必須）
1. 対象プロジェクトの `knowledge/[プロジェクト名]/INDEX.md` を読む
2. `knowledge/[プロジェクト名]/errors.md` で既知エラーを確認する
3. `knowledge/[プロジェクト名]/workflow.md` で手順を確認する

### 作業中
- エラー発生時: まず `errors.md` を確認し、既知エラーかどうかを調べる
- 新しいエラー・事象が発生した場合: 作業を続けながらメモしておく

### 作業終了時（必須）
- 新しい知見・エラー・判断があれば、該当ナレッジファイルに追記する
  - 新エラー → `errors.md` に追記
  - フロー変更・手順変更 → `workflow.md` を更新
  - シート構成変更（ダイヤ改正等）→ `csv-rules.md` と `INDEX.md` の表を更新
- `updated` フロントマターの日付を更新する
- `memory/daily/YYYY-MM-DD.md` にも `[INSIGHT]` / `[ERROR]` として記録する（[[dashboard]] 経由）

## 使用するスキル

| スキル | 用途 |
|--------|------|
| [[excel-parse/SKILL\|excel-parse]] | ExcelファイルからデータをJSON形式で抽出 |
| [[figma-fetch/SKILL\|figma-fetch]] | FigmaデザインからテキストノードをJSON形式で取得 |
| [[diff-check/SKILL\|diff-check]] | 2つのJSONデータを照合し差分リストを生成 |
| [[report-gen/SKILL\|report-gen]] | 差分リストをMarkdownレポートに変換して出力 |

## 参照ファイル

- [[agent-registry]] — 全エージェント登録情報
- [[dashboard]] — メモリ・エクスペリエンス層ダッシュボード
- [[SPRINT-FLOW]] — スプリントフロー全体・全エージェント共通ルール（タイムアウト等）

## 作業フロー

### 作業開始時
```
ユーザーの依頼（GitHub Issue）を受け取る
    ↓
① Issue のラベルを「⏳ status: waiting」→「🔄 status: in-progress」に更新
② config.json と figma/urls.json でチェック対象シート一覧を確認
③ チェック順序を決定（例: 南1 → 南2 → 南3 → ... → 北15）
```

### 各シートのチェック（全シート完了するまでループ）
```
対象シート（例: 南1_20260322）
    ↓
④ excel-parse: Excel のシートデータ → JSON（抽出データ）
⑤ figma-fetch: 平日・土曜・祝日の Figma URL → JSON（デザインデータ）
⑥ diff-check: 2つのJSONを照合 → 差分リスト
⑦ report-gen: 差分リスト → reports/YYYY-MM-DD_<シート名>_report.md に保存
⑧ GitHub Issue にチェック結果をコメント投稿
⑨ 次のシートへ → ④に戻る
```

### 全シート完了後
```
⑩ 全体サマリーを GitHub Issue にコメント投稿
⑪ Issue のラベルを「🔄 status: in-progress」→「✅ status: done」に更新
⑫ Issue をクローズ
```

## メモリ記録の義務

以下の場面では [[dashboard]] を通じて `memory/daily/YYYY-MM-DD.md` に記録する:
- **チェック完了時**: 件数・差分数・重大度サマリー → `[INSIGHT]`
- **パースエラー発生時**: Excelフォーマット変化・Figma構造変化 → `[ERROR]`
- **繰り返し発見されるパターン**: 英語表記ミスの傾向など → `[PATTERN]`

## ルール

- レポートのファイル名は `YYYY-MM-DD_<シート名>_report.md` 形式で `reports/` に保存する
- 差分がゼロの場合も「異常なし」レポートを出力する
- Figmaデータ取得には必ず `figma/urls.json` に登録されたURLのみ使用する
- **1シートチェックごと**に GitHub Issue へコメント投稿する（途中経過を残す）
- **全シート完了後**に Issue ラベルを `✅ status: done` に更新してクローズする
- チェック中断・エラー発生時は Issue にその旨をコメントして `⏳ status: waiting` に戻す
