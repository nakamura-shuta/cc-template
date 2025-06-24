# Claude Code カスタムコマンド

このディレクトリには、プロジェクト専用のカスタムスラッシュコマンドが含まれています。

## 利用可能なコマンド

### `/project:execute-task`
ワークフローの各フェーズに対応した効率的なタスク実行をサポートします。

**使用方法:**
```
/project:execute-task <phase> <task_description>
または
/project:execute-task <task_description>  # 自動実行モード
```

**引数:**
- `phase`: 実行するフェーズ (explore, plan, code, document, commit, cleanup) ※省略可能
- `task_description`: タスクの説明

**自動実行モード:**
フェーズを指定しない場合、以下の順番で自動実行されます：
explore → plan → code → cleanup

**各フェーズの動作:**

1. **explore** - コードベース探索
   - 関連ファイルの検索（Glob、Grep使用）
   - コードパターンの分析
   - 既存実装の調査

2. **plan** - 設計・計画フェーズ
   - `.work/${sessionId}/task.md`に設計ドキュメント作成
   - 実装計画の策定
   - 必要な変更点のリストアップ

3. **code** - TDD実装フェーズ
   - テストを先に作成（TDD方式）
   - 実装コードの作成
   - テスト実行（npm test、cargo test等）

4. **document** - ドキュメント更新
   - 関連ドキュメントの更新
   - README.mdやCLAUDE.mdの修正

5. **commit** - 変更のコミット
   - git statusで変更確認
   - 適切なコミットメッセージでコミット

6. **cleanup** - 作業ファイルのクリーンアップ
   - `.work`内の古いファイル削除
   - 作業状況のサマリー

**使用例:**
```bash
# 自動実行モード
/project:execute-task "user authentication feature"

# 個別フェーズ実行
/project:execute-task explore "authentication"
/project:execute-task plan "JWT token implementation"
/project:execute-task code "auth_test"
/project:execute-task document "API docs"
/project:execute-task commit "feat: add JWT auth"
/project:execute-task cleanup
```

### `/project:refactor-analysis`
現在のコードベースを分析し、リファクタリング計画を立案します。

**使用方法:**
```
/project:refactor-analysis
```

**出力:**
- `docs/refactor.md` にリファクタリング計画を生成

**分析内容:**
- コード品質の評価
- パフォーマンスの観点
- 保守性の向上
- セキュリティの観点
- 技術的負債

## カスタムコマンドの追加方法

1. このディレクトリに新しい `.md` ファイルを作成
2. ファイル名がコマンド名になります（例: `analyze-deps.md` → `/project:analyze-deps`）
3. ファイル内にプロンプトや指示を記述
4. `$ARGUMENTS` プレースホルダーを使用して引数を受け取ることができます

## 命名規則

- ケバブケース（kebab-case）を使用
- 動詞-名詞の形式を推奨（例: `analyze-deps`, `generate-docs`）
- 明確で説明的な名前を使用


## ユーザー単位のカスタムコマンド

ユーザー単位でのカスタムコマンドは
`~/.claude/commands`ディレクトリに配置して以下のように
実行してください。

```
/user:<command-name> [arguments]

```

## References
https://docs.anthropic.com/en/docs/claude-code/slash-commands