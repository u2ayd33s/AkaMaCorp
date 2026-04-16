---
project: chofu-station-signage
tags: [knowledge, chofu-station-signage, errors, troubleshooting]
created: 2026-04-16
updated: 2026-04-16
---

# 調布駅バス時刻表サイネージ — エラー集・トラブルシューティング

## 既知エラー一覧

### E-001: Figma API タイムアウト

| 項目 | 内容 |
|------|------|
| 原因 | Figma MCP の `get_design_context` を使用した |
| 症状 | 大規模ノードでリクエストがタイムアウトする |
| 解決策 | **Figma REST API** に切り替える |
| 発生日 | 2026-03（初回チェック時） |

```python
# NG: get_design_context は使用禁止
# OK: REST API を直接呼び出す
url = f"https://api.figma.com/v1/files/{FILE_KEY}/nodes"
resp = requests.get(url, headers={"X-Figma-Token": FIGMA_TOKEN}, ...)
```

---

### E-002: 日本語文字化け

| 項目 | 内容 |
|------|------|
| 原因 | Windows の stdout エンコーディングがデフォルトで cp932 |
| 症状 | ターミナル出力の日本語が文字化けする |
| 解決策 | スクリプト冒頭で stdout を UTF-8 に変更 |

```python
import sys, io
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8')
```

---

### E-003: 行先が次セクションのデータで上書き

| 項目 | 内容 |
|------|------|
| 原因 | `parse_sec` の検索範囲が広すぎてセクション境界を超えた |
| 症状 | 平日の「行先」が土曜のデータで上書きされる |
| 解決策 | `sorted_starts` でセクション境界を取得して範囲を制限する |
| 注意 | Excelの枠番号・行先行はセクション先頭から `+35行` 以内で検索 |

---

### E-004: 時刻が1時間ずれる

| 項目 | 内容 |
|------|------|
| 原因 | 5時台が空の場合に時間帯の grouping がずれる |
| 症状 | Figmaの6時台データがExcelの5時台と比較される |
| 解決策 | timetable instance ベースの traversal を使用（index基準ではなく） |

---

### E-005: 南1の構造が他シートと異なる

| 項目 | 内容 |
|------|------|
| 原因 | 南1は旧形式（平日/土曜/日祝 が別ノード） |
| 症状 | 他シートと同じロジックで処理するとノードが見つからない |
| 解決策 | urls.json の形式を確認し、南1専用の取得ロジックを適用する |
| 備考 | 南2以降は1ノードに全スケジュール統合（新形式） |

---

## チェックポイント（作業前確認）

- [ ] `FIGMA_TOKEN` 環境変数が設定されているか
- [ ] `urls.json` に対象シートのnodeIdが全て登録されているか
- [ ] `config.json` の `frameCount` が最新ダイヤ改正に合っているか

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（WORKFLOW.md よくあるエラーから移植） |
