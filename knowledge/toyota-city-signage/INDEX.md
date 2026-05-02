---
project: toyota-city-signage
tags: [knowledge, toyota-city-signage, index]
created: 2026-04-16
updated: 2026-04-16
---

# 豊田市バス時刻表サイネージ — ナレッジINDEX

ExcelデータとFigmaデザインの差分を自動チェックするプロジェクト。

## プロジェクト概要

| 項目 | 内容 |
|------|------|
| リポジトリ | `u2ayd33s/DiscrepancyCheck` |
| データパス | `DiscrepancyCheck/projects/toyota-city-signage/` |
| Figma fileKey | `figma/urls.json` の `fileKey` を参照（要登録） |
| 担当エージェント | [[08_discrepancy-check/CLAUDE\|08_discrepancy-check]] |

## チェック対象項目

| 項目 | 英語表記チェック |
|------|----------------|
| バス停名 | ✅ |
| 路線名 | ✅ |
| 行先名 | ✅ |
| 運行時刻 | — |
| 英語ラベル全般 | — |

## 対象シート

> 初回チェック時に確定させ、下表を更新すること。

| シート | 平日 | 土曜 | 日祝 | 合計N |
|--------|------|------|------|-------|
| 豊田市① | — | — | — | — |
| 豊田市② | — | — | — | — |
| 豊田市③ | — | — | — | — |
| 豊田市⑤ | — | — | — | — |
| 豊田市駅東口 | — | — | — | — |

> ⚠️ CSVファイルの存在から上記5シートが対象と推定。config.json で確定すること。

## ナレッジ一覧

| ファイル | 内容 |
|---------|------|
| [[toyota-city-signage/workflow\|workflow]] | 差分チェック作業フロー・手順 |
| [[toyota-city-signage/csv-rules\|csv-rules]] | CSV書き出しルール |
| [[toyota-city-signage/errors\|errors]] | よくあるエラーと解決策 |

## 調布駅との共通ナレッジ

基本ロジック・ワークフローは調布駅と共通。差分は以下:
- [[chofu-station-signage/figma-structure]] — Figmaノード構造（調布駅版、豊田市で構造差異があれば本プロジェクトのナレッジを更新）
- [[chofu-station-signage/errors]] — E-001〜E-004は豊田市でも同様に適用される

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（DiscrepancyCheck/projects から移植） |
