---
project: chofu-station-signage
tags: [knowledge, chofu-station-signage, index]
created: 2026-04-16
updated: 2026-04-16
---

# 調布駅バス時刻表サイネージ — ナレッジINDEX

ExcelデータとFigmaデザインの差分を自動チェックするプロジェクト。

## プロジェクト概要

| 項目 | 内容 |
|------|------|
| リポジトリ | `u2ayd33s/DiscrepancyCheck` |
| データパス | `DiscrepancyCheck/projects/chofu-station-signage/` |
| Figma fileKey | `zCjchuHb9Aw3v1gXsHzV0i` |
| 担当エージェント | [[08_discrepancy-check/CLAUDE\|08_discrepancy-check]] |

## チェック対象項目

| 項目 | 英語表記チェック |
|------|----------------|
| バス停名 | ✅ |
| 路線名 | ✅ |
| 行先名 | ✅ |
| 運行時刻 | — |
| 英語ラベル全般 | — |

## 対象シート（2026-04-01改正版）

| シート | 平日 | 土曜 | 日祝 | 合計N |
|--------|------|------|------|-------|
| 南1 | 4 | 4 | 4 | 12 |
| 南2 | 2 | 2 | 2 | 6 |
| 南3 | 4 | 4 | 6 | 14 |
| 南4 | 3 | 4 | 2 | 9 |
| 北11 | 6 | 5 | 5 | 16 |
| 北12 | 8 | 7 | 7 | 22 |
| 北13 | 5 | 4 | 4 | 13 |
| 北14 | 3 | 4 | 4 | 11 |
| 北15 | 2 | 2 | 2 | 6 |

## ナレッジ一覧

| ファイル | 内容 |
|---------|------|
| [[chofu-station-signage/workflow\|workflow]] | 差分チェック作業フロー・手順 |
| [[chofu-station-signage/csv-rules\|csv-rules]] | CSV書き出しルール |
| [[chofu-station-signage/errors\|errors]] | よくあるエラーと解決策 |
| [[chofu-station-signage/figma-structure\|figma-structure]] | Figmaノード構造・マッピング仕様 |

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（DiscrepancyCheck/projects から移植） |
