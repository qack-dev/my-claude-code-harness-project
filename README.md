# my-claude-code-harness-project

> どんなプロジェクトでも、AIエージェントが最小限の質問だけで自走的に完遂できるようにする、ハーネス(harness、AIが迷わず作業できるようリポジトリに文脈・規約・検証手段を埋め込む設計)テンプレート集。

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![CI](https://github.com/qack-dev/my-claude-code-harness-project/actions/workflows/ci.yml/badge.svg)](https://github.com/qack-dev/my-claude-code-harness-project/actions/workflows/ci.yml)
![Node.js](https://img.shields.io/badge/node-%3E%3D18-brightgreen)

## 概要

新しいプロジェクトを始めるたびに、AIエージェント向けの `CLAUDE.md` や `docs/` を毎回ゼロから書き起こすのは手間がかかります。本プロジェクトは、その「ハーネス」一式(行動指針・要件定義・タスク管理・検証ループ)を、プレースホルダ入りのテンプレートとして提供します。

新規プロジェクトのルートに `PROJECT_BRIEF.md`(プロジェクト詳細の記入用紙)を用意し、Claude Codeで `/init` と入力するだけで、そのプロジェクト用の `CLAUDE.md` / `docs/*.md` / `.claude/commands/*.md` が生成される状態を目指します。

本リポジトリ自体は実行可能なアプリケーションコードを持たない、Markdown/JSONのテンプレート集です。

**このREADMEには、性質の異なる2種類の使い方が出てきます。混同しないよう区別してください。**

| | 対象 | 誰が読むか |
| --- | --- | --- |
| [ハーネスを新規プロジェクトに使う](#ハーネスを新規プロジェクトに使う) | 本リポジトリの**外側**にある、これから作る別のプロジェクト | 本リポジトリのテンプレートを利用したい人(メインの使い方) |
| [本リポジトリ自体を開発する](#本リポジトリ自体を開発する) | 本リポジトリ**自身**(`templates/`の中身を直す) | 本リポジトリのテンプレートを改修したい人 |

## 主な機能

- `templates/questions.json`: プロジェクト詳細の質問セット定義(唯一の"真実の源"。`PROJECT_BRIEF.md`や各`.template`の`{{KEY}}`は、すべてここで定義されたキーに対応する)
- `templates/PROJECT_BRIEF.md.template`: 新規プロジェクトのルートに置く、プロジェクト詳細の記入用紙の雛形
- `templates/commands/init.md.template`: `PROJECT_BRIEF.md`から`CLAUDE.md`/`docs/*.md`/`.claude/commands/*.md`一式を生成する`/init`コマンドの雛形(ユーザースコープのグローバルコマンドとして使う)
- `templates/CLAUDE.md.template`: AIエージェントの行動指針(9項目)を含む汎用CLAUDE.mdの雛形
- `templates/docs/*.template`: PRD / ARCHITECTURE / TASKS / ADR の雛形
- `templates/commands/{plan,verify,commit}.md.template`: 生成先プロジェクトで使う plan / verify / commit スラッシュコマンドの雛形
- `tests/validate-templates.mjs`: テンプレートのプレースホルダと質問定義の整合性を検証するスクリプト(本リポジトリの保守用)
- `.github/`: 本リポジトリのCI設定、Issue/PRテンプレートの雛形

## ハーネスを新規プロジェクトに使う

### 初回セットアップ(開発者本人のマシンごとに1回だけ)

1. 本リポジトリをclone(未実施の場合)

   ```bash
   git clone https://github.com/qack-dev/my-claude-code-harness-project.git
   ```

2. `templates/commands/init.md.template` を、**Claude Codeのユーザースコープのコマンドディレクトリ**(プロジェクトごとの`.claude/commands/`ではなく、ホームディレクトリ配下の`.claude/commands/`)へ `init.md` としてコピーする
3. コピーした`init.md`冒頭の `ハーネスリポジトリのローカルパス: [要確認: ...]` を、手順1でcloneした実際のローカルパスに書き換える

これで、以後どの新規プロジェクトでも `/init` が自動的に使えるようになります(プロジェクトごとの再セットアップは不要)。

### 新規プロジェクトを始めるたび

1. 新規プロジェクトのルートに `templates/PROJECT_BRIEF.md.template` を `PROJECT_BRIEF.md` としてコピーし、分かる範囲で人の手で記入する(分からない項目は空欄のままでよい)
2. 新規プロジェクトのディレクトリでClaude Codeを起動し、`/init` と入力する
3. `/init` が `PROJECT_BRIEF.md` を読み、
   - 未記入の項目
   - 記入されていても、内容が曖昧・矛盾・情報不足だとAIが判断した項目

   についてだけ対話で確認し、揃ったら `CLAUDE.md` / `docs/PRD.md` / `docs/ARCHITECTURE.md` / `docs/TASKS.md` / `docs/adr/0001-....md` / `.claude/commands/{plan,verify,commit}.md` を生成する

出力例(`PROJECT_BRIEF.md`の「プロジェクトの目的」「想定ユーザー」を埋めた場合、`templates/CLAUDE.md.template`の該当箇所がこう生成されます):

```markdown
## 1. プロジェクト概要

個人開発者が複数プロジェクトのTODOを1画面で横断管理できるようにする。

想定ユーザー: 個人開発者(自分自身)
```

生成後、`CLAUDE.md`に残っている`[要確認: 内容]`(テスト/リント/ビルドコマンドなど、テンプレートが機械的に埋められない箇所)は、プロジェクトの実態に合わせて自分で埋めてください。

## 本リポジトリ自体を開発する

`templates/`配下のテンプレートそのものを改修する場合の手順です。上記の「ハーネスを新規プロジェクトに使う」とは目的が異なります。

### 動作要件

- OS: Windows / macOS / Linux(いずれも動作確認 `[要確認: 実際に確認したOSを記載]`)
- Node.js: 18以上 `[要確認: 動作確認済みの正確なバージョン]`
- インターネット接続: `npm run lint` の初回実行時に `markdownlint-cli2` を取得するため必要

Node.js/npmは、**本リポジトリ自身(テンプレート集)の整合性チェックとMarkdown lintのためだけ**に使われています。`templates/questions.json`のキー定義と`.template`ファイル内の`{{KEY}}`が一致しているかを検証する`tests/validate-templates.mjs`がNode.js標準モジュールのみで実装されているためです。生成先の新規プロジェクトの技術スタックとは無関係で、生成物である`CLAUDE.md`/`docs/*.md`自体はどんな言語・フレームワークのプロジェクトにも使えます。

### クイックスタート

```bash
cd my-claude-code-harness-project
npm install
npm run verify
```

`npm run verify` が成功すれば、セットアップは完了です。

### 開発コマンド

```bash
npm install       # セットアップ(追加依存パッケージなし)
npm run lint       # 全Markdownファイルの構文チェック
npm test           # templates/questions.json と *.template の整合性チェック
npm run verify      # lint + test をまとめて実行
```

新しい質問キーを追加する場合は、`templates/questions.json` にキーを追加したうえで、対応する `.template` ファイルに `{{KEY}}` を追記してください。`npm test` が両者の整合性(未定義キーの使用・未使用キーの検出)をチェックします。`PROJECT_BRIEF.md.template`や`init.md.template`のように、人が手で埋める箇所は`{{KEY}}`ではなく`[要確認: 内容]`記法を使ってください(この記法は`npm test`の対象外です)。

### プロジェクト構成

```text
my-claude-code-harness-project/
├── .claude/commands/        本リポジトリ保守用の定型コマンド(plan/verify/commit)
├── .github/                 CI設定、Issue/PRテンプレート
├── docs/                    本リポジトリ自体のPRD/ARCHITECTURE/TASKS/ADR
├── templates/               配布物本体(汎用ハーネステンプレート、{{KEY}}プレースホルダ入り)
│   ├── questions.json       質問セット定義(唯一の"真実の源")
│   ├── PROJECT_BRIEF.md.template   生成先プロジェクトのルートに置く記入用紙
│   ├── CLAUDE.md.template
│   ├── docs/*.template
│   └── commands/*.template  init(グローバル)/ plan / verify / commit
├── tests/                   テンプレート整合性チェックスクリプト
├── CLAUDE.md                本リポジトリ用のAI行動指針
└── CONTRIBUTING.md
```

## トラブルシューティング

| 症状 | 原因と対処法 |
| --- | --- |
| `/init` を実行しても「初回セットアップが未完了」と言われる | ユーザースコープの`init.md`内の「ハーネスリポジトリのローカルパス」が`[要確認: ...]`のままです。「初回セットアップ」の手順3を実施してください |
| `/init` を実行しても `PROJECT_BRIEF.md` が見つからないと言われる | 新規プロジェクトのルートに `PROJECT_BRIEF.md` がありません。`templates/PROJECT_BRIEF.md.template` をコピーして配置してください |
| `npm run lint` がネットワークエラーで失敗する | `npx markdownlint-cli2` が初回にパッケージを取得できていません。インターネット接続を確認するか、オフライン運用が必要な場合は `devDependencies` としての固定インストールに切り替えてください(`docs/TASKS.md` 参照) |
| `npm test` が「未定義のプレースホルダ」エラーを出す | `.template` ファイル内で使った `{{KEY}}` が `templates/questions.json` に定義されていません。質問定義を追加するか、タイプミスを修正してください |
| テンプレートを適用したのに `{{KEY}}` が残ったままになる | `PROJECT_BRIEF.md` の該当項目が空欄のままだった可能性があります。`/init` 実行時の対話で回答したか確認してください |

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
