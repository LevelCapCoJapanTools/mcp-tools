# MCPツール層 — docs/mcp

このディレクトリは `mcp-tools` リポジトリが管理する **MCPツール定義の参照ドキュメント** です。
ツール定義の実体は `.claude/mcp/` 配下の YAML ファイルです。

## 管理パス

| パス | 内容 |
| --- | --- |
| `.claude/mcp/**` | ツール定義 YAML（実体） |
| `docs/mcp/**` | ツール定義の参照ドキュメント（本ディレクトリ） |

## セット一覧

| セット | ファイル | 含まれるツール |
| --- | --- | --- |
| basic | `sets/basic.yml` | shell_execute, filesystem_read |
| full | `sets/full.yml` | shell_execute, filesystem_read, http_request, database_query |

## ツール一覧

| ツール名 | 定義ファイル | 実行種別 | 概要 |
| --- | --- | --- | --- |
| shell_execute | `.claude/mcp/shell.yml` | shell | 許可リスト内コマンドをsandbox実行 |
| filesystem_read | `.claude/mcp/filesystem.yml` | filesystem | sandbox内ファイルの読み取り |
| http_request | `.claude/mcp/http.yml` | http | 許可ホストへのHTTPリクエスト |
| database_query | `.claude/mcp/database.yml` | database | 読み取り専用のSQLクエリ実行 |

## ツール定義の必須要素

すべてのツール定義 YAML は以下の要素を持つ。

| フィールド | 説明 |
| --- | --- |
| `name` | ツール名（一意） |
| `input_schema` | 入力スキーマ（JSON Schema） |
| `output_schema` | 出力スキーマ（JSON Schema） |
| `execution` | 実行方法と実行種別 |
| `permissions` | 最小権限設定 |
| `timeout` | タイムアウト秒数とアクション |
| `errors` | エラーコードと説明 |

## セキュリティ方針

- すべてのツールは sandbox 前提で実行する
- 権限は最小限（read-only を基本とする）
- timeout は必須。超過時は即時強制終了またはキャンセル
- 任意コマンド実行禁止。shell_execute は allow_list で管理する。
- 認証情報はペイロードに含めず、環境変数経由で注入する
- 入力は実行前にスキーマ検証を行い、不正な入力は即時 400 を返す
- パストラバーサル・SQLインジェクションなどは定義レベルで禁止

## 配付ルール

- `sets/*.yml` の include に列挙されたパスをそのまま target repo へコピーする
- path remap は禁止
- include は glob のみ（regex 禁止）
- セット依存は禁止（各セットは単独で完結する）

## 参照

- [ssot-distribution-system-spec-final-2026-04-06.md](../ssot-distribution-system-spec-final-2026-04-06.md) — 配付システム全体仕様（第19章: MCP catalog 設計）
- [catalog-path-ownership-draft.md](../catalog-path-ownership-draft.md) — catalog path 担当表
