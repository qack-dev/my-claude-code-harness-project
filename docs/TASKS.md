# TASKS: my-claude-code-harness-project

AIエージェントは着手前に必ずこのファイルを読み、着手するタスクと完了条件(Doneの定義)を確認すること。
タスク完了時は該当のチェックボックスに印を入れ、新たに判明した残タスクがあれば追記すること。

## 進行中

(なし)

## 未着手

- [ ] `templates/` を実プロジェクトへ適用した動作確認(ドッグフーディング)
  - 完了条件: 適当なダミープロジェクトに対して`templates/questions.json`の質問に回答し、`templates/*.template`から実際に`CLAUDE.md`/`docs/*.md`/`.claude/commands/*.md`を生成できることを1回通しで確認する
- [ ] Node.jsの動作確認済みバージョンを確定し、`CLAUDE.md`・`README.md`・`package.json`の`[要確認]`を解消する
  - 完了条件: `node -v`で実際に確認したバージョンを記載し、`engines`フィールドを追加する
- [ ] オフライン環境向けに`markdownlint-cli2`を`devDependencies`として固定インストールする方式へ切り替えるか判断する
  - 完了条件: 判断結果を`docs/adr/`に新規ADRとして記録する(現状維持の場合もその理由を記録する)
- [ ] `templates/questions.json`の質問セットを実際に2〜3件の新規プロジェクトで使ってみて、質問数・粒度が適切か検証する
  - 完了条件: 使用結果をもとに質問セットを更新するか、変更不要と判断してその旨をコミットメッセージまたはADRに残す

## 完了

- [x] リポジトリの基盤ファイル一式(`.gitignore`/`.env.example`/`LICENSE`/`.editorconfig`/`.markdownlint.jsonc`/`package.json`)を作成
  - 完了条件: 各ファイルが存在し、秘密情報を含まない
- [x] `templates/`配下(汎用ハーネス本体)を作成
  - 完了条件: `questions.json`、`CLAUDE.md.template`、`docs/*.template`、`commands/*.template`が揃い、プレースホルダの命名が統一されている
- [x] 本リポジトリ用の`CLAUDE.md`・`docs/`・`.claude/commands/`を作成
  - 完了条件: `CLAUDE.md`が9項目すべてを含み、記載コマンドが実行可能である
- [x] 品質保証一式(`tests/validate-templates.mjs`、CI、Issue/PRテンプレート、`CONTRIBUTING.md`)を作成
  - 完了条件: `npm run verify`が実際に成功する
- [x] `README.md`を作成
  - 完了条件: 13セクションが揃い、クイックスタートのコマンドが実際に動作する
- [x] Issue #1「READMEの使い方が分かりにくい」への対応: プロジェクト詳細の入力方式を「PROJECT_BRIEF.md記入 + グローバル`/init` + 空欄/曖昧な項目のみ対話」に統一し、README.mdを全面改修
  - 完了条件: `templates/PROJECT_BRIEF.md.template`・`templates/commands/init.md.template`・`docs/adr/0002-project-brief-and-global-init-command.md`を追加し、`README.md`/`docs/PRD.md`/`docs/ARCHITECTURE.md`/`templates/questions.json`/`CLAUDE.md`を新フローに統一。`npm run verify`が通り、一時ディレクトリでのダミープロジェクト生成テストで`{{KEY}}`置換が過不足なく解決することを確認済み

## スコープ外(やらないこと)

`docs/PRD.md` の「スコープ外」を参照。
