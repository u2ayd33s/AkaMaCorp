---
project: chofu-station-signage
tags: [knowledge, chofu-station-signage, workflow]
created: 2026-04-16
updated: 2026-04-16
---

# 調布駅バス時刻表サイネージ — 差分チェック ワークフロー

## 全体フロー

```
① GitHub Issue を受け取る → in-progress ラベルに変更
    ↓
② Figma REST API でノードデータを取得 → JSON に保存
    ↓
③ Python スクリプトで Excel と比較 → 差分リストを生成
    ↓
④ シートごとに reports/ へ Markdown レポートを保存（ラウンド番号付き）
    ↓
⑤ GitHub Issue にシートごとの結果をコメント投稿（第Nラウンド と明示）
    ↓
⑥ 全シートで差分あり？
    ├─ Yes → fixing ラベルに変更 → 全体サマリー＋修正依頼コメント投稿
    │          ↓（修正完了の連絡を受けたら）
    │         ② に戻る（ラウンド番号をインクリメント）
    └─ No  → 全OKサマリーコメント投稿 → done ラベルに変更 → Issue クローズ
```

### ラベル状態遷移

| 状態 | ラベル |
|------|--------|
| チェック待ち | `⏳ status: waiting` |
| チェック中 | `🔄 status: in-progress` |
| 修正待ち | `🔧 status: fixing` |
| 完了 | `✅ status: done` |

---

## ステップ詳細

### ① GitHub Issue の準備

- 対象リポジトリ: `u2ayd33s/DiscrepancyCheck`

```bash
gh issue edit <番号> --repo u2ayd33s/DiscrepancyCheck \
  --remove-label "⏳ status: waiting" --add-label "🔄 status: in-progress"
```

---

### ② Figma データ取得

> **重要**: Figma MCP の `get_design_context` は大規模ノードでタイムアウトするため**使用禁止**。
> 代わりに **Figma REST API** を直接呼び出す。

```python
import requests, json

FIGMA_TOKEN = "figd_..."
FILE_KEY    = "zCjchuHb9Aw3v1gXsHzV0i"
NODE_IDS    = ["3504:13349", ...]  # urls.json の nodeId

url = f"https://api.figma.com/v1/files/{FILE_KEY}/nodes"
resp = requests.get(url,
    headers={"X-Figma-Token": FIGMA_TOKEN},
    params={"ids": ",".join(NODE_IDS)}
)
data = resp.json()
# 保存先: C:/Users/u2ayd/AppData/Local/Temp/figma_remaining.json
```

- `urls.json` に全シートの nodeId が登録されている
- 南1は旧形式（平日/土曜/日祝 が別ノード）、南2以降は **1ノードに全スケジュール統合**

---

### ③ 差分チェックスクリプト

スクリプト: `C:/Users/u2ayd/AppData/Local/Temp/check_all.py`

詳細な仕様は [[chofu-station-signage/figma-structure]] を参照。

---

### ④ レポート保存

- ファイル名規則: `YYYY-MM-DD_全シート_report.md`（全シート統合版のみ）
- 保存先: `DiscrepancyCheck/projects/chofu-station-signage/reports/`
- 個別シートのレポートファイルは作成しない
- 再チェックで差分が解消された場合は**上書き**（最終状態のみ保持）

---

### ⑤ GitHub Issue へのコメント投稿

コメントには必ず **第Nラウンド** を明記する。

```bash
# シートごとの結果
gh issue comment <番号> --repo u2ayd33s/DiscrepancyCheck --body "## 第1ラウンド チェック結果 - <シート名> ..."

# 差分あり → fixing ラベル
gh issue edit <番号> --repo u2ayd33s/DiscrepancyCheck \
  --remove-label "🔄 status: in-progress" --add-label "🔧 status: fixing"

# 全OK → done + クローズ
gh issue edit <番号> --repo u2ayd33s/DiscrepancyCheck \
  --remove-label "🔄 status: in-progress" --add-label "✅ status: done"
gh issue close <番号> --repo u2ayd33s/DiscrepancyCheck
```

---

## 参照データ一覧

| データ | 場所 |
|--------|------|
| Figma nodeId 一覧 | `figma/urls.json` |
| Excel ファイル | `data/excel/調布駅サイネージ時刻表画面生成マクロ.xlsm` |
| Figma fileKey | `zCjchuHb9Aw3v1gXsHzV0i` |
| Personal Access Token | 環境変数 `FIGMA_TOKEN` 推奨 |

## 更新履歴

| 日付 | 更新者 | 内容 |
|------|--------|------|
| 2026-04-16 | 07_discrepancy-check | 初版作成（WORKFLOW.md から移植） |
