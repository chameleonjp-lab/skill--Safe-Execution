# RTK Safe Execution Skill

**AIが読むコマンド出力を短縮しながら、元の出力と終了コードを保存するCodex用Skillです。**

このリポジトリはSkillの**共通管理元**です。ゲームなど別のリポジトリでは、そのリポジトリの `.agents/skills/rtk-safe-execution/` にSkill一式を配置するまで自動では利用できません。

## 含まれるもの

- [SKILL.md](.agents/skills/rtk-safe-execution/SKILL.md)：適用範囲・禁止事項・実行手順
- `scripts/run.py`：対象コマンドを1回実行し、完全な出力を保存してから `rtk log` で表示用の短い結果を作成
- `scripts/verify.py`：保存した元ログのSHA-256とサイズを確認
- `tests/test_run.py`：成功・失敗・RTK不在・機密情報の可能性・タイムアウト等を確認するオフラインテスト
- [参考資料](.agents/skills/rtk-safe-execution/references/usage-and-ci.md)：Codex Cloud・CI・記録の保存期限に関する説明

## 設計上の約束

1. **元ログが先**：コマンドの標準出力と標準エラーを、Gitの作業ツリー外にそのまま保存します。
2. **検証判定は変更しない**：RTKの結果とは無関係に、元コマンドの終了コードを返します。
3. **重要な記録は圧縮しない**：正式な監査、認証・秘密情報、GitHub Actionsの公式検証は既存手順を維持します。
4. **障害時に戻れる**：短縮結果が不十分なら、保存済みの完全ログを確認します。再実行を前提にしません。
5. **無断導入をしない**：RTKが見つからない場合はその旨を表示し、勝手にインストールしません。

## 動作条件

- Python **3.11以上**（標準ライブラリのみ）
- RTK本体は**任意**。RTK不在でも元ログを保存しますが、短縮表示は実行できません。
- RTKを使う場合、公式配布元 [rtk-ai/rtk](https://github.com/rtk-ai/rtk) から別途導入し、`rtk --version` を確認します。

## このリポジトリでの検証

```bash
python3 -m unittest discover -s .agents/skills/rtk-safe-execution/tests -v
```

付属テストはRTK本体がなくても動きます。`rtk` の代わりに模擬コマンドを使用するため、**実際のRTKとの互換性や削減率は別途検証が必要**です。

## 導入先リポジトリでの使い方

まず、このリポジトリ内の `.agents/skills/rtk-safe-execution/` を導入先リポジトリの同じパスにコピーし、必要な場合のみ導入先の `AGENTS.md` に [案内例](AGENTS-addendum.example.md) を追記します。既存のルールを上書きしません。レビュー可能なPRで導入してください。

導入先のルートで、既存の正式なテストコマンドを確認してから実行します。

```bash
SKILL_DIR=.agents/skills/rtk-safe-execution
python3 "$SKILL_DIR/scripts/run.py" --kind test --label unit -- npm test
python3 "$SKILL_DIR/scripts/run.py" --kind list --label tracked-files -- git ls-files
```

実行ごとに `raw.log`、`metadata.json`、RTKで短縮できた場合は `rtk-summary.txt` が作られます。通常は `~/.local/state/rtk-safe-execution/` などのGit管理外に保存します。完全ログを読み返すには `metadata.json` に示された実行記録を確認してください。

```bash
python3 "$SKILL_DIR/scripts/verify.py" /path/to/metadata.json
```

## Codex Cloud・ChatGPT Work

Codexがこのリポジトリを作業対象にした場合、リポジトリ内のSkillを参照できます。**この共通管理リポジトリに置いただけでは、別のゲームリポジトリにSkillが配られるわけではありません**。利用先へPRで導入してください。

Codex Cloudの実行環境でRTK本体が利用できるか、また自動適用フックが動くかは別途確認が必要です。本Skillはフックを有効にしません。ChatGPT WorkではPython実行環境が利用できる場合に限り実行スクリプトを使えます。

## ログ保存と安全性

完全ログは実行環境の**ローカルファイル**です。クラウドの作業が終わると失われる場合があります。長期保存が必要なら、機密情報、公開範囲、保存期間を確認したうえで別途保管してください。GitHubへ自動アップロードしたり、公式CIログをRTKで置換したりしません。

警告のために一部の秘密情報らしいパターンを検査しますが、完全な秘密情報検出ではありません。認証情報が出力され得るコマンドでは使用しないでください。

## 保守方針

変更時は必ず付属テストを実行し、元ログ、終了コード、機密情報の扱いを確認します。mainへの反映はPRでレビューしてから行います。

※ 本リポジトリはRTK公式のものではなく、RTKを安全に補助利用する独立したSkillです。
