# ARCHITECTURE: my-claude-code-harness-project

## 全体構成

実行コードを持たない、Markdown/JSONのテンプレート集。整合性チェックのみNode.js標準モジュールで実装する。

```text
templates/                         配布物本体(汎用・プレースホルダ入り)
├── questions.json                 質問定義(唯一の"真実の源")
├── PROJECT_BRIEF.md.template      生成先プロジェクトのルートに置く記入用紙
├── CLAUDE.md.template
├── docs/*.template
└── commands/
    ├── init.md.template           ユーザースコープのグローバルコマンド(生成処理本体)
    └── {plan,verify,commit}.md.template  生成先プロジェクトで使うコマンド

tests/validate-templates.mjs       questions.json と *.template の整合性を検証
  ↓ 参照
templates/questions.json ── keys ──→ *.template 内の {{KEY}} と突き合わせ
```

## データの流れ

1. 開発者はマシンごとに1回だけ、`templates/commands/init.md.template` をClaude Codeのユーザースコープのコマンドディレクトリへ `init.md` としてコピーし、本ハーネスリポジトリのローカルパスを書き込む(初回セットアップ)
2. 新規プロジェクトを始めるたびに、`templates/PROJECT_BRIEF.md.template` を対象プロジェクトの `PROJECT_BRIEF.md` としてコピーし、分かる範囲で人が手で記入する
3. 対象プロジェクトでClaude Codeを起動し `/init` と入力すると、`init.md` が `PROJECT_BRIEF.md` を読み、`questions.json` の各キーについて (a) 空欄、または (b) 記入されているが曖昧・矛盾・情報不足とAIが判断したもの、だけを対話で確認する
4. 全キーが揃ったら、`init.md` に書かれたローカルパスから `templates/CLAUDE.md.template` / `templates/docs/*.template` / `templates/commands/{plan,verify,commit}.md.template` を読み込み、`{{KEY}}` を回答で置換して対象プロジェクトの `CLAUDE.md` / `docs/*.md` / `.claude/commands/*.md` として配置する
5. 本リポジトリ自体では、`tests/validate-templates.mjs` が `questions.json` のキー集合と、全 `.template` ファイル中に出現する `{{KEY}}` の集合を突き合わせ、未定義キーの使用がないかを検証する(`PROJECT_BRIEF.md.template` や `init.md.template` 内の `[要確認: 内容]` は人が手で埋める箇所であり、この検証の対象外)

## 主要な設計判断

| 決定 | 理由 | 詳細 |
| --- | --- | --- |
| 実行可能なCLIツールではなく、静的テンプレート集として構成する | 対象プロジェクトの技術スタックを本リポジトリ側で先に決め打ちしないため。技術スタックの決定は各プロジェクト立ち上げ時の質問(`TECH_STACK`)に委ねる | `docs/adr/0001-record-architecture-decisions.md` |
| プレースホルダは`{{KEY}}`形式とし、`[要確認: 内容]`マーカーと明確に区別する | 「後で機械的に埋める値」と「人間/AIの判断が必要な値」を混同しないため | 同上 |
| プロジェクト詳細の一次情報源を対話ではなく`PROJECT_BRIEF.md`とし、`/init`はユーザースコープのグローバルコマンドとして1回だけセットアップする | 新規プロジェクトを頻繁に立ち上げる利用者が、毎回の対話やコマンドファイルのコピーを繰り返さずに済むようにするため | `docs/adr/0002-project-brief-and-global-init-command.md` |

## 既知の制約

- `npm run lint` は初回実行時に `npx` が `markdownlint-cli2` をネットワーク経由で取得するため、オフライン環境では失敗する `[要確認: 社内ネットワーク等オフライン運用が必要な場合は事前インストール方式への変更を検討]`
