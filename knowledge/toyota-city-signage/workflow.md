---
project: toyota-city-signage
tags: [knowledge, toyota-city-signage, workflow]
created: 2026-04-16
updated: 2026-04-16
---

# 豊田市バス時刻表サイネージ — 差分チェック ワークフロー

> 基本フローは調布駅と共通。[[chofu-station-signage/workflow]] を先に読むこと。
> 豊田市固有の差異のみここに記載する。

## 全体フロー

調布駅と同一（[[chofu-station-signage/workflow#全体フロー]] 参照）。

---

## 豊田市固有の設定

### Figma データ取得

```python
FIGMA_TOKEN = "figd_..."
FILE_KEY    = ""          # urls.json の fileKey（初回チェック前に登録必須）
NODE_IDS    = ["xxxx:yyyy", ...]

# 保存先: C:/Users/u2ayd/AppData/Local/Temp/toyota_figma.json
```

> `urls.json` に全シートの nodeId を登録してから実行すること。

### 差分チェックスクリプト

スクリプト: `C:/Users/u2ayd/AppData/Local/Temp/toyota_check_all.py`（初回実装時に作成）

> Figmaノード構造が調布駅と異なる場合は、[[toyota-city-signage/INDEX]] の「対象シート」表と本ファイルを更新する。

### レポート保存先

`DiscrepancyCheck/projects/toyota-city-signage/reports/`

---

## 初回セットアップ時のTODO

- [ ] `figma/urls.json` に fileKey と全シートの nodeId を登録
- [ ] `config.json` の `frameCount` に各シートの枠数を登録
- [ ] Figmaノード構造を確認し、調布駅と構造差異があれば [[toyota-city-signage/INDEX]] を更新
- [ ] 対象シート一覧を [[toyota-city-signage/INDEX]] の表に記入

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（WORKFLOW.md から移植） |
