---
name: power-automate-tips
description: Power Automate と SharePoint・Microsoft 365 の連携パターンを提供するスキル。"Power Automate", "フロー", "自動化", "承認ワークフロー", "通知", "トリガー", "SharePoint 連携", "Teams 通知" などのキーワードで使用する。フロー設計のパターン・JSON設定・HTTP アクション構成を提示する。
---

# Power Automate 連携パターン

SharePoint・Microsoft 365 との Power Automate フロー設計パターンを提供します。

## よく使うトリガー

| トリガー | 用途 |
|---------|------|
| SharePoint — アイテムが作成されたとき | リスト追加時の自動処理 |
| SharePoint — アイテムが変更されたとき | 更新検知・承認フロー起動 |
| 手動でフローをトリガーする | ボタン起動・テスト用 |
| スケジュール済みクラウドフロー | 定期バッチ処理 |
| HTTP 要求を受け取ったとき | SharePoint JS からのキック |

---

## パターン集

### パターン1: リスト追加 → Teams 通知
```
トリガー: SharePoint「アイテムが作成されたとき」
  ↓
アクション: Teams「チャットまたはチャンネルでメッセージを投稿する」
  チャンネル: 通知先チャンネル
  メッセージ: 「📋 新規申請: @{triggerOutputs()?['body/Title']}
               申請者: @{triggerOutputs()?['body/Author/DisplayName']}
               リンク: @{triggerOutputs()?['body/_ServerUrl']}」
```

### パターン2: 承認ワークフロー
```
トリガー: SharePoint「アイテムが作成されたとき」
  ↓
アクション: 承認「承認を開始して待機する」
  種類: 承認/拒否 — 最初に応答
  担当者: 承認者のメールアドレス
  タイトル: @{triggerOutputs()?['body/Title']}
  ↓
条件分岐:
  [承認] → SharePoint「アイテムの更新」: 承認ステータス = "承認済"
            Teams 通知: 「✅ 承認されました」
  [拒否] → SharePoint「アイテムの更新」: 承認ステータス = "却下"
            Teams 通知: 「❌ 却下されました: @{outputs('承認を開始して待機する')?['body/responseSummary']}」
```

### パターン3: SharePoint JS から HTTP でフロー起動
```javascript
// Power Automate 側: 「HTTP 要求を受け取ったとき」トリガーを使用
// JSON スキーマ例:
// { "type": "object", "properties": { "itemId": { "type": "integer" }, "title": { "type": "string" } } }

async function triggerFlow(flowUrl, payload) {
  const response = await fetch(flowUrl, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload)
  });
  return response.ok;
}

// 使用例
await triggerFlow('https://prod-xx.westus.logic.azure.com/...', {
  itemId: 123,
  title: '申請タイトル'
});
```

### パターン4: 定期バッチ — 期限切れアイテムの通知
```
トリガー: スケジュール（毎日 午前9時）
  ↓
アクション: SharePoint「複数の項目を取得」
  フィルタークエリ: DueDate le '@{utcNow()}' and Status ne '完了'
  ↓
Apply to each（各アイテム）:
  → Teams 通知 または メール送信
```

### パターン5: ファイル追加 → PDF 変換 → 別ライブラリへコピー
```
トリガー: SharePoint「ファイルが作成されたとき（プロパティのみ）」
  ↓
アクション: OneDrive「ファイルの変換」
  ファイル: @{triggerOutputs()?['body/Id']}
  対象形式: PDF
  ↓
アクション: SharePoint「ファイルの作成」
  フォルダーパス: /承認済みPDF
  ファイル名: @{triggerOutputs()?['body/FileLeafRef']} .pdf
  ファイルコンテンツ: @{body('ファイルの変換')}
```

---

## HTTP アクションで Graph API を呼ぶ

Power Automate 内で Graph API を直接叩く場合:

```
アクション: HTTP
  メソッド: GET
  URI: https://graph.microsoft.com/v1.0/me/joinedTeams
  認証: Active Directory OAuth
    テナント: @{variables('TenantId')}
    対象ユーザー: https://graph.microsoft.com
    クライアント ID: @{variables('ClientId')}
    資格情報の種類: Secret
    シークレット: @{variables('ClientSecret')}
```

---

## エラーハンドリングのベストプラクティス

1. **スコープ + 実行条件** — 各アクションに `Configure run after` で失敗時の分岐を設定
2. **Compose で変数化** — 繰り返し使う値は `Compose` アクションで変数化
3. **終了アクション** — エラー時は `Terminate` アクションでフロー停止 + ログ記録
4. **再試行ポリシー** — HTTP アクションには再試行ポリシー（指数バックオフ）を設定
