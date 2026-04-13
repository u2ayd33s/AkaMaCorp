---
date: 2026-03-16
type: error
title: agent-registry のパス記述がファイルシステムと乖離
tags: [error, agent-registry, maintenance]
status: resolved
---

## 何が起きたか
agent-registry.md のパスが `agents/sharepoint/` と記述されているが、実際のディレクトリは `agents/03_sharepoint/` に変更された（番号プレフィックス付与 PR後）。

## 修正した方向
README と agent-registry を実際のディレクトリ構造に合わせて更新する（2026-03-19実施）。

## 再発防止原則
エージェントディレクトリのリネーム・移動時は必ず agent-registry.md と README.md も同時に更新する。PR作成前にチェックリストとして確認する。
