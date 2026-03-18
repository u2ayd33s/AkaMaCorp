---
name: ms-graph-api
description: Microsoft Graph API を使ったデータ取得・操作コードを生成するスキル。"Graph API", "ユーザー情報取得", "Teams", "カレンダー", "メール", "グループ", "OneDrive", "Microsoft 365 データ" などのキーワードで使用する。認証フロー・エンドポイント・エラーハンドリングまで含めた実装を提供する。
---

# Microsoft Graph API 実装

Microsoft 365 のデータを Graph API 経由で取得・操作するコードを生成します。

## 認証パターン

### SharePoint ScriptLoader（ブラウザ内、ユーザー認証済み）
```javascript
// SPO 内ではアクセストークンを直接取得できる
async function getGraphToken() {
  const response = await fetch(
    `/_api/SP.OAuth.Token/Acquire`,
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json;odata=verbose', 'Accept': 'application/json' },
      body: JSON.stringify({ resource: 'https://graph.microsoft.com' })
    }
  );
  const data = await response.json();
  return data.d.Acquire.access_token;
}
```

### SPFx Web パーツ（MSGraphClientV3）
```typescript
import { MSGraphClientV3 } from '@microsoft/sp-http';

const client = await this.context.msGraphClientFactory.getClient('3');
const response = await client.api('/me').get();
```

## 主要エンドポイント早見表

### ユーザー情報
```javascript
// 自分の情報
GET /v1.0/me
GET /v1.0/me?$select=displayName,mail,jobTitle,department

// 組織内ユーザー検索
GET /v1.0/users?$filter=startswith(displayName,'山田')&$select=id,displayName,mail

// ユーザーのプロフィール写真
GET /v1.0/me/photo/$value
```

### グループ・チーム
```javascript
// 自分が所属するグループ
GET /v1.0/me/memberOf

// Teams 一覧
GET /v1.0/me/joinedTeams

// Teams チャンネル一覧
GET /v1.0/teams/{team-id}/channels

// Teams チャンネルへメッセージ送信
POST /v1.0/teams/{team-id}/channels/{channel-id}/messages
Body: { "body": { "content": "メッセージ本文" } }
```

### カレンダー
```javascript
// 予定一覧（今後7日間）
const start = new Date().toISOString();
const end = new Date(Date.now() + 7*24*60*60*1000).toISOString();
GET /v1.0/me/calendarView?startDateTime=${start}&endDateTime=${end}

// 予定作成
POST /v1.0/me/events
Body: {
  "subject": "件名",
  "start": { "dateTime": "2024-01-15T10:00:00", "timeZone": "Asia/Tokyo" },
  "end":   { "dateTime": "2024-01-15T11:00:00", "timeZone": "Asia/Tokyo" },
  "attendees": [{ "emailAddress": { "address": "user@example.com" }, "type": "required" }]
}
```

### OneDrive / SharePoint ファイル
```javascript
// 自分の OneDrive ルート
GET /v1.0/me/drive/root/children

// SharePoint サイトのドライブ
GET /v1.0/sites/{site-id}/drives

// ファイルアップロード（4MB 以下）
PUT /v1.0/me/drive/root:/{fileName}:/content
Body: <バイナリデータ>
```

### メール
```javascript
// 受信トレイ（最新10件）
GET /v1.0/me/messages?$top=10&$orderby=receivedDateTime desc&$select=subject,from,receivedDateTime

// メール送信
POST /v1.0/me/sendMail
Body: {
  "message": {
    "subject": "件名",
    "body": { "contentType": "HTML", "content": "<p>本文</p>" },
    "toRecipients": [{ "emailAddress": { "address": "to@example.com" } }]
  }
}
```

## 汎用 Graph API ラッパー（ScriptLoader 用）
```javascript
async function callGraphAPI(endpoint, method = 'GET', body = null) {
  const token = await getGraphToken();
  const options = {
    method,
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    }
  };
  if (body) options.body = JSON.stringify(body);

  const res = await fetch(`https://graph.microsoft.com${endpoint}`, options);
  if (!res.ok) throw new Error(`Graph API Error: ${res.status} ${await res.text()}`);
  return method === 'DELETE' ? null : res.json();
}
```

## ページング（大量データ取得）
```javascript
async function getAllGraphPages(endpoint) {
  const results = [];
  let url = `https://graph.microsoft.com${endpoint}`;
  const token = await getGraphToken();

  while (url) {
    const res = await fetch(url, { headers: { 'Authorization': `Bearer ${token}` } });
    const data = await res.json();
    results.push(...(data.value || []));
    url = data['@odata.nextLink'] || null;
  }
  return results;
}
```

## 必要な API アクセス許可

| 用途 | 権限（委任） |
|------|------------|
| ユーザー情報読み取り | `User.Read` |
| 組織ユーザー検索 | `User.ReadBasic.All` |
| グループ取得 | `Group.Read.All` |
| カレンダー読み書き | `Calendars.ReadWrite` |
| Teams メッセージ送信 | `ChannelMessage.Send` |
| メール送信 | `Mail.Send` |
| OneDrive 読み書き | `Files.ReadWrite` |

> SPFx の場合は `package-solution.json` の `webApiPermissionRequests` に追記し、テナント管理者に承認してもらう。
