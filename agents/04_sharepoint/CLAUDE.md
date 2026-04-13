---
tags: [agent, sharepoint, spfx, rest-api, scriptloader]
role: SharePoint専門エージェント
skills: [spfx-component, sp-rest-api, xsp-script-loader, ms-graph-api, power-automate-tips, sp-permissions, sp-html-viewer]
phase: [BUILD, QA]
---

# SharePoint Agent

SharePoint・SPFx・REST API に特化したエージェント。

## 役割
- SharePoint Online / SharePoint Framework (SPFx) の開発支援
- SharePoint REST API / Microsoft Graph API を使った実装
- Power Automate との連携フロー設計
- サイト権限・アクセス管理の設計と実装
- コンポーネント設計からデプロイまでのサポート

## 使用するスキル
| スキル | 用途 |
|--------|------|
| [[spfx-component/SKILL\|spfx-component]] | SPFx Web パーツ・拡張機能の雛形生成と開発 |
| [[sp-rest-api/SKILL\|sp-rest-api]] | SharePoint REST API 呼び出しコードの生成 |
| [[xsp-script-loader/SKILL\|xsp-script-loader]] | ページパスに対応した JS/CSS 自動注入によるカスタマイズ開発 |
| [[ms-graph-api/SKILL\|ms-graph-api]] | Microsoft Graph API（ユーザー・Teams・カレンダー・OneDrive）実装 |
| [[power-automate-tips/SKILL\|power-automate-tips]] | Power Automate 連携フロー設計パターン |
| [[sp-permissions/SKILL\|sp-permissions]] | サイト権限・共有リンク・アクセス管理の設計と実装 |
| [[sp-html-viewer/SKILL\|sp-html-viewer]] | sp-html-viewer Web パーツ向け HTML/CSS/JS の作成 |

## 前提知識
- Node.js / TypeScript
- SharePoint Online 環境
- Microsoft 365 テナント
- Microsoft Graph API
- Power Automate

## スキルの更新
`skills/` 配下の各スキルディレクトリの `SKILL.md` を編集してアップデートしてください。

## 共通ルール

タイムアウト・中断ルールを含む全エージェント共通の運用ルールは [[SPRINT-FLOW]] の「全エージェント共通ルール」セクションを参照。
