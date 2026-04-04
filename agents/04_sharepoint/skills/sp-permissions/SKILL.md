---
name: sp-permissions
description: SharePoint のサイト権限・共有リンク・アクセス管理のベストプラクティスとコードを提供するスキル。"権限", "アクセス制御", "共有リンク", "サイト権限", "グループ管理", "閲覧のみ", "外部共有", "権限継承" などのキーワードで使用する。REST API / Graph API による権限操作コードと設計指針を提供する。
---

# SharePoint 権限・アクセス管理

SharePoint のサイト・リスト・アイテム単位の権限設計と REST API 実装を提供します。

## 権限レベル早見表

| 権限レベル | 内容 | 用途 |
|-----------|------|------|
| フルコントロール | 全操作 + 権限管理 | サイト管理者 |
| デザイン | ページ・テーマ変更 | デザイン担当 |
| 編集 | リスト・アイテムの追加・編集・削除 | 一般編集者 |
| 投稿 | アイテムの追加・編集（削除不可） | 申請者 |
| 閲覧 | 読み取り専用 | 参照のみ |

---

## REST API による権限操作

### サイトグループ一覧取得
```javascript
async function getSiteGroups(siteUrl) {
  const res = await fetch(`${siteUrl}/_api/web/sitegroups`, {
    headers: { 'Accept': 'application/json;odata=nometadata' }
  });
  const data = await res.json();
  return data.value; // [{ Id, Title, Description }]
}
```

### ユーザーをグループに追加
```javascript
async function addUserToGroup(siteUrl, groupId, userLoginName) {
  const requestDigest = await getRequestDigest(siteUrl);
  await fetch(`${siteUrl}/_api/web/sitegroups(${groupId})/users`, {
    method: 'POST',
    headers: {
      'Accept': 'application/json;odata=nometadata',
      'Content-Type': 'application/json;odata=nometadata',
      'X-RequestDigest': requestDigest
    },
    body: JSON.stringify({ LoginName: userLoginName })
  });
}
```

### ユーザーをグループから削除
```javascript
async function removeUserFromGroup(siteUrl, groupId, userId) {
  const requestDigest = await getRequestDigest(siteUrl);
  await fetch(`${siteUrl}/_api/web/sitegroups(${groupId})/users/removebyid(${userId})`, {
    method: 'POST',
    headers: { 'X-RequestDigest': requestDigest }
  });
}
```

### リストの権限継承を解除して独自権限を設定
```javascript
async function breakListPermissions(siteUrl, listTitle) {
  const requestDigest = await getRequestDigest(siteUrl);

  // 権限継承を解除（既存の権限をコピーして独立させる）
  await fetch(
    `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/breakroleinheritance(copyRoleAssignments=true,clearSubscopes=true)`,
    { method: 'POST', headers: { 'X-RequestDigest': requestDigest } }
  );
}
```

### アイテム単位の権限設定
```javascript
async function setItemPermission(siteUrl, listTitle, itemId, principalId, roleDefId) {
  const requestDigest = await getRequestDigest(siteUrl);

  // アイテムの権限継承を解除
  await fetch(
    `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/items(${itemId})/breakroleinheritance(copyRoleAssignments=false,clearSubscopes=true)`,
    { method: 'POST', headers: { 'X-RequestDigest': requestDigest } }
  );

  // ロール割り当て追加
  await fetch(
    `${siteUrl}/_api/web/lists/getbytitle('${listTitle}')/items(${itemId})/roleassignments/addroleassignment(principalid=${principalId},roledefid=${roleDefId})`,
    { method: 'POST', headers: { 'X-RequestDigest': requestDigest } }
  );
}
// roleDefId: 1073741829=フルコントロール, 1073741830=編集, 1073741827=投稿, 1073741826=閲覧
```

---

## 共有リンク管理（Graph API）

### 共有リンクの作成
```javascript
async function createSharingLink(siteId, itemId, linkType = 'view') {
  // linkType: 'view'（閲覧）, 'edit'（編集）, 'embed'（埋め込み）
  const token = await getGraphToken();
  const res = await fetch(
    `https://graph.microsoft.com/v1.0/sites/${siteId}/drive/items/${itemId}/createLink`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        type: linkType,
        scope: 'organization' // 'anonymous'（外部共有）, 'organization'（組織内のみ）
      })
    }
  );
  const data = await res.json();
  return data.link.webUrl;
}
```

### 共有リンクの一覧・削除
```javascript
// 共有リンク一覧取得
async function getPermissions(siteId, itemId) {
  const token = await getGraphToken();
  const res = await fetch(
    `https://graph.microsoft.com/v1.0/sites/${siteId}/drive/items/${itemId}/permissions`,
    { headers: { 'Authorization': `Bearer ${token}` } }
  );
  return (await res.json()).value;
}

// 共有リンク削除
async function deletePermission(siteId, itemId, permissionId) {
  const token = await getGraphToken();
  await fetch(
    `https://graph.microsoft.com/v1.0/sites/${siteId}/drive/items/${itemId}/permissions/${permissionId}`,
    { method: 'DELETE', headers: { 'Authorization': `Bearer ${token}` } }
  );
}
```

---

## 権限設計のベストプラクティス

1. **グループで管理する** — 個人ではなく M365 グループ・セキュリティグループで権限付与
2. **最小権限の原則** — 必要最低限の権限のみ付与（編集不要なら閲覧のみ）
3. **継承を乱用しない** — アイテム単位の権限破棄は管理コストが高い。設計段階で整理する
4. **外部共有は組織ポリシーに従う** — `scope: 'anonymous'` は管理者設定で無効化されている場合がある
5. **棚卸し定期実施** — 退職者・異動者のグループ削除を Power Automate で自動化することを推奨

---

## ユーザーの権限確認
```javascript
async function checkUserPermissions(siteUrl) {
  const res = await fetch(
    `${siteUrl}/_api/web/effectiveBasePermissions`,
    { headers: { 'Accept': 'application/json;odata=nometadata' } }
  );
  const perms = await res.json();
  // perms.High / perms.Low でビットフラグを確認
  // 例: 編集権限チェック
  const canEdit = (parseInt(perms.Low) & 0x4) !== 0;
  return { canEdit };
}
```
