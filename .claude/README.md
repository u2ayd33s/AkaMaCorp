# .claude/

Claude Code がこの Vault を開いたときに参照する設定・拡張フォルダ。

## フォルダ構成

| フォルダ | 用途 | 状態 |
|---------|------|------|
| `agents/` | Claude Code サブエージェント定義（`.md` ファイルで定義） | 予約済み・未使用 |
| `commands/` | カスタムスラッシュコマンド（`/コマンド名` で呼び出せる `.md` ファイル） | 予約済み・未使用 |
| `skills/` | Claude Code スキル定義（Vault 固有のスキルを置く） | 予約済み・未使用 |

> **注意**: `agents/` はここに置くとClaude Codeのサブエージェント機能に使われる。  
> AkaMaCorp 独自のエージェント定義は `/agents/` ルートフォルダで管理しており別物。

## ファイル

| ファイル | 用途 |
|---------|------|
| `settings.local.json` | このマシン固有のClaude Code設定（`.gitignore` 対象） |
