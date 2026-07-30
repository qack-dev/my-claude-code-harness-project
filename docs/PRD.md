# PRD: my-claude-code-harness-project

## 目的

どんなソフトウェアプロジェクトでも、Claude Codeのようなアプリ開発の自動化ツールが最小限の質問だけで自律的に完遂できるよう、文脈・規約・検証ループ・タスクの4条件を満たす「ハーネス」一式(`CLAUDE.md`・`docs/`・`.claude/commands/`のテンプレート)を提供する。

## 想定ユーザー

複数の新規プロジェクトを頻繁に立ち上げる開発者(`qack-dev`本人)。案件ごとにハーネス整備をゼロから作り直さずに済ませたい人。

## 機能要件

- `templates/questions.json` による、プロジェクト詳細の最小質問セット定義
  - 受け入れ条件: 8つの質問キー(PROJECT_NAME/PROJECT_PURPOSE/TECH_STACK/MAIN_FEATURES/TARGET_USERS/GITHUB_ACCOUNT/VISIBILITY/LICENSE)が定義されている
- `templates/PROJECT_BRIEF.md.template` による、プロジェクト詳細の記入用紙の提供
  - 受け入れ条件: `questions.json`の8質問すべてに対応する記入欄を含む
  - 前提: プロジェクト詳細の一次情報源は対話ではなくこのファイルであり、AIエージェント(`/init`)は空欄または内容が曖昧・矛盾・情報不足と判断した項目についてのみ対話で確認する(詳細は`docs/adr/0002-project-brief-and-global-init-command.md`)
- `templates/commands/init.md.template` による、`PROJECT_BRIEF.md`からハーネス一式を生成する`/init`コマンドの提供
  - 受け入れ条件: ユーザースコープのグローバルコマンドとして1回セットアップすれば、以後どの新規プロジェクトでも再セットアップなしに使える
- `templates/CLAUDE.md.template` による、汎用CLAUDE.mdの雛形提供
  - 受け入れ条件: 元の指示にある9項目すべての見出しを含む
- `templates/docs/*.template` による、PRD/ARCHITECTURE/TASKS/ADRの雛形提供
  - 受け入れ条件: 4種のテンプレートファイルが存在し、`{{KEY}}`プレースホルダが`questions.json`のキーと一致する
- `templates/commands/*.template` による、init/plan/verify/commitスラッシュコマンドの雛形提供
  - 受け入れ条件: 4つのコマンドテンプレートが存在する
- テンプレートの整合性を自動検証する仕組み
  - 受け入れ条件: `npm test`が実際に実行でき、パスする

## 非機能要件

- パフォーマンス: 該当なし(静的ファイル集のため)
- セキュリティ: 秘密情報を一切含まない。`.env.example`はダミー値のみ
- 可用性/対象環境: Node.js `[要確認: 動作確認済みバージョン]` が動作するOS全般(Windows/macOS/Linux)

## スコープ外

- 実際に対象プロジェクトへテンプレートを自動展開するCLIツールの実装(現時点では手動/AIエージェントによるコピー&置換を前提とする)
- 特定言語・フレームワーク向けの実装コード生成

## 公開・ライセンス

- GitHubアカウント: qack-dev
- 公開範囲: public
- ライセンス: MIT
