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
1. Explore → 2. Plan → 3. Code → 4. Cleanup

**実行フェーズ:**
1. **Explore** - コードベース探索
   ```
   /project:execute-task explore "user authentication"
   ```
   
2. **Plan** - 設計・計画フェーズ
   ```
   /project:execute-task plan "implement JWT authentication"
   ```
   
3. **Code** - TDD実装フェーズ
   ```
   /project:execute-task code "test_auth_module"
   ```
   
4. **Document** - ドキュメント更新
   ```
   /project:execute-task document "API documentation"
   ```
   
5. **Commit** - 変更のコミット
   ```
   /project:execute-task commit "feat: add JWT authentication"
   ```
   
6. **Cleanup** - 作業ファイルのクリーンアップ
   ```
   /project:execute-task cleanup
   ```

**主な機能:**
- ripgrepによる高速検索
- セッションID管理での作業ファイル整理
- JS/TS/Rust対応のテスト・型チェック
- Git操作の簡略化
- 古い作業ファイルの自動削除

**使用例:**
```bash
# 自動実行モード（explore → plan → code → cleanup）
/project:execute-task "user authentication feature"

# 個別フェーズ実行
/project:execute-task explore "authentication"
/project:execute-task plan "JWT token implementation"
/project:execute-task code "auth_test"
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