# CONTRIBUTING

## 開発環境のセットアップ

```bash
git clone [要確認: リポジトリURL].git
cd my-claude-code-harness-project
npm install
```

## 変更の進め方

1. `docs/TASKS.md` を確認し、着手するタスクを決める(未記載の作業であればタスクを追記する)
2. `templates/` を変更する場合は、`templates/questions.json` のキー定義との整合性を保つ(`{{KEY}}`は必ず`questions.json`に定義があること)
3. 変更後、以下を実行して通過を確認する

   ```bash
   npm run verify
   ```

4. `docs/TASKS.md` を更新する(該当タスクにチェックを入れる、または新規に判明したタスクを追記する)
5. Pull Requestを作成する(`.github/PULL_REQUEST_TEMPLATE.md` のチェックリストに従う)

## コミットメッセージ

「何を変更したか」ではなく「なぜ変更したか」を意識して、簡潔に記載してください。

## 行動規範

節度を持ち、Issue/PRでは技術的な議論に集中してください。
