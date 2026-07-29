# ARCHITECTURE: my-claude-code-harness-project

## 全体構成

実行コードを持たない、Markdown/JSONのテンプレート集。整合性チェックのみNode.js標準モジュールで実装する。

```text
templates/                         配布物本体(汎用・プレースホルダ入り)
├── questions.json                 質問定義(唯一の"真実の源")
├── CLAUDE.md.template
├── docs/*.template
└── commands/*.template

tests/validate-templates.mjs       questions.json と *.template の整合性を検証
  ↓ 参照
templates/questions.json ── keys ──→ *.template 内の {{KEY}} と突き合わせ
```

## データの流れ

1. AIエージェントが新規プロジェクトに着手する際、`templates/questions.json` の質問を順にユーザーへ尋ねる
2. 得られた回答で `templates/*.template` の `{{KEY}}` を置換し、対象プロジェクトの `CLAUDE.md` / `docs/*.md` / `.claude/commands/*.md` として配置する
3. 本リポジトリ自体では、`tests/validate-templates.mjs` が `questions.json` のキー集合と、全 `.template` ファイル中に出現する `{{KEY}}` の集合を突き合わせ、未定義キーの使用がないかを検証する

## 主要な設計判断

| 決定 | 理由 | 詳細 |
| --- | --- | --- |
| 実行可能なCLIツールではなく、静的テンプレート集として構成する | 対象プロジェクトの技術スタックを本リポジトリ側で先に決め打ちしないため。技術スタックの決定は各プロジェクト立ち上げ時の質問(`TECH_STACK`)に委ねる | `docs/adr/0001-record-architecture-decisions.md` |
| プレースホルダは`{{KEY}}`形式とし、`[要確認: 内容]`マーカーと明確に区別する | 「後で機械的に埋める値」と「人間/AIの判断が必要な値」を混同しないため | 同上 |

## 既知の制約

- `npm run lint` は初回実行時に `npx` が `markdownlint-cli2` をネットワーク経由で取得するため、オフライン環境では失敗する `[要確認: 社内ネットワーク等オフライン運用が必要な場合は事前インストール方式への変更を検討]`
