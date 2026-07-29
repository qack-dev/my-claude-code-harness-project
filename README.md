# my-claude-code-harness-project

> どんなプロジェクトでも、AIエージェントが最小限の質問だけで自走的に完遂できるようにする、ハーネス(harness、AIが迷わず作業できるようリポジトリに文脈・規約・検証手段を埋め込む設計)テンプレート集。

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![CI](https://github.com/qack-dev/my-claude-code-harness-project/actions/workflows/ci.yml/badge.svg)](https://github.com/qack-dev/my-claude-code-harness-project/actions/workflows/ci.yml)
![Node.js](https://img.shields.io/badge/node-%3E%3D18-brightgreen)

[要確認: CIバッジのリンクは、実際にGitHubへpushしてActionsが1回実行された後に正しく表示されます]

## 概要

新しいプロジェクトを始めるたびに、AIエージェント向けの `CLAUDE.md` や `docs/` を毎回ゼロから書き起こすのは手間がかかります。本プロジェクトは、その「ハーネス」一式(行動指針・要件定義・タスク管理・検証ループ)を、プレースホルダ入りのテンプレートとして提供します。

`templates/questions.json` に定義された8つの質問にAIエージェントが答えを埋めるだけで、その場でプロジェクト固有の `CLAUDE.md` / `docs/*.md` / `.claude/commands/*.md` を生成できる状態を目指します。

本リポジトリ自体は実行可能なアプリケーションコードを持たない、Markdown/JSONのテンプレート集です。

## 主な機能

- `templates/questions.json`: 新規プロジェクト立ち上げ時にAIエージェントが尋ねる最小質問セット定義
- `templates/CLAUDE.md.template`: AIエージェントの行動指針(9項目)を含む汎用CLAUDE.mdの雛形
- `templates/docs/*.template`: PRD / ARCHITECTURE / TASKS / ADR の雛形
- `templates/commands/*.template`: plan / verify / commit スラッシュコマンドの雛形
- `tests/validate-templates.mjs`: テンプレートのプレースホルダと質問定義の整合性を検証するスクリプト
- `.github/`: CI設定、Issue/PRテンプレートの雛形

## 動作要件

- OS: Windows / macOS / Linux(いずれも動作確認 `[要確認: 実際に確認したOSを記載]`)
- Node.js: 18以上 `[要確認: 動作確認済みの正確なバージョン]`(`npm run lint` の `npx markdownlint-cli2` 実行、および `npm test` に使用)
- インターネット接続: `npm run lint` の初回実行時に `markdownlint-cli2` を取得するため必要

## クイックスタート

```bash
git clone https://github.com/qack-dev/my-claude-code-harness-project.git
cd my-claude-code-harness-project
npm install
npm run verify
```

`npm run verify` が成功すれば、セットアップは完了です。

## 使い方

新しいプロジェクトにハーネスを適用する最小の例です。

1. `templates/questions.json` の8つの質問(`PROJECT_NAME`, `PROJECT_PURPOSE`, `TECH_STACK`, `MAIN_FEATURES`, `TARGET_USERS`, `GITHUB_ACCOUNT`, `VISIBILITY`, `LICENSE`)への回答を用意する
2. AIエージェント(Claude Code等)に、`templates/CLAUDE.md.template` と `templates/docs/*.template` を読み込ませ、`{{KEY}}` を回答内容で置換した上で、対象プロジェクトの `CLAUDE.md` / `docs/PRD.md` / `docs/ARCHITECTURE.md` / `docs/TASKS.md` として保存させる
3. 同様に `templates/commands/*.template` を対象プロジェクトの `.claude/commands/*.md` として保存させる

出力例(`templates/CLAUDE.md.template` の一部を埋めた場合):

```markdown
## 1. プロジェクト概要

個人開発者が複数プロジェクトのTODOを1画面で横断管理できるようにする。

想定ユーザー: 個人開発者(自分自身)
```

## プロジェクト構成

```text
my-claude-code-harness-project/
├── .claude/commands/        本リポジトリ保守用の定型コマンド(plan/verify/commit)
├── .github/                 CI設定、Issue/PRテンプレート
├── docs/                    本リポジトリ自体のPRD/ARCHITECTURE/TASKS/ADR
├── templates/               配布物本体(汎用ハーネステンプレート、{{KEY}}プレースホルダ入り)
│   ├── questions.json       最小質問セット定義
│   ├── CLAUDE.md.template
│   ├── docs/*.template
│   └── commands/*.template
├── tests/                   テンプレート整合性チェックスクリプト
├── CLAUDE.md                本リポジトリ用のAI行動指針
└── CONTRIBUTING.md
```

## 開発者向け

```bash
npm install       # セットアップ(追加依存パッケージなし)
npm run lint       # 全Markdownファイルの構文チェック
npm test           # templates/questions.json と *.template の整合性チェック
npm run verify      # lint + test をまとめて実行
```

新しい質問キーを追加する場合は、`templates/questions.json` にキーを追加したうえで、対応する `.template` ファイルに `{{KEY}}` を追記してください。`npm test` が両者の整合性(未定義キーの使用・未使用キーの検出)をチェックします。

## トラブルシューティング

| 症状 | 原因と対処法 |
| --- | --- |
| `npm run lint` がネットワークエラーで失敗する | `npx markdownlint-cli2` が初回にパッケージを取得できていません。インターネット接続を確認するか、オフライン運用が必要な場合は `devDependencies` としての固定インストールに切り替えてください(`docs/TASKS.md` 参照) |
| `npm test` が「未定義のプレースホルダ」エラーを出す | `.template` ファイル内で使った `{{KEY}}` が `templates/questions.json` に定義されていません。質問定義を追加するか、タイプミスを修正してください |
| テンプレートを適用したのに `{{KEY}}` が残ったままになる | AIエージェントへの指示時に、置換対象のキーと回答の対応が渡っていない可能性があります。`templates/questions.json` の全キーに対する回答が揃っているか確認してください |

## ロードマップ

- [ ] `templates/` を実プロジェクトへ適用する動作確認(ドッグフーディング)
- [ ] Node.jsの動作確認済みバージョンの確定
- [ ] オフライン環境向けのlint実行方式の検討
- [ ] 質問セットの粒度の見直し(複数プロジェクトでの実地検証後)

詳細は [docs/TASKS.md](docs/TASKS.md) を参照してください。

## コントリビュート・ライセンス・謝辞

コントリビュート方法は [CONTRIBUTING.md](CONTRIBUTING.md) を参照してください。

本プロジェクトは [MIT License](LICENSE) のもとで公開されています(公開範囲: public)。

このハーネス設計は、AIエージェントに文脈・規約・検証ループ・タスクをリポジトリ構造そのもので伝えるという考え方に基づいています。
