# Claude Code カスタムコマンド

このディレクトリには、プロジェクト専用のカスタムスラッシュコマンドが含まれています。

## 利用可能なコマンド

### `/project:commit`
プロジェクトの変更を適切なフォーマットでコミットします。

**使用方法:**
```
/project:commit
/project:commit --no-verify
```

**機能:**
- 自動的に事前チェック（lint、build、generate:docs）を実行
- ステージングされていない場合は自動的にgit add
- 変更内容を分析して適切なコミットメッセージを生成
- 絵文字付きConventional Commitフォーマットを使用
- 大きな変更の場合は複数のコミットに分割を提案

**オプション:**
- `--no-verify`: 事前チェックをスキップ

### `/project:dump`
現在のClaude Codeセッションの会話履歴をログファイルとして出力します。

**使用方法:**
```
/project:dump
```

**出力:**
- `.claude/logs/claude-code-session-{YYYY-MM-DD_HH-mm-ss}.md` にセッション履歴を保存
- ユーザーとClaudeのやり取りをMarkdown形式で記録

### `/project:test`
テスト用のカスタムコマンドです。引数の受け渡しとコンテキスト情報の確認に使用します。

**使用方法:**
```
/project:test <argument>
```

**機能:**
- 渡された引数を表示
- ファイルパスが渡された場合はそのコンテンツも表示
- 単なる確認用コマンド

### `/project:ask`
コードベースに関する質問に答える読み取り専用コマンドです。

**使用方法:**
```
/project:ask <質問内容>
```

**機能:**
- コード解析と動作説明
- ファイル構造の調査と検索
- エラーの診断と問題解析
- ドキュメントの参照と解説
- `.work`ディレクトリ内にのみ分析結果を保存可能

**特徴:**
- `.work`以外への書き込み操作を制限（読み取り専用）
- #ultrathinkを使用した詳細な回答
- 非破壊的な調査と分析に特化

### `/project:gemini-search`
Google Gemini CLIを使用してWeb検索を実行します。

**使用方法:**
```
/project:gemini-search <検索クエリ>
```

**機能:**
- Gemini CLIを通じたWeb検索
- 日本語検索に対応
- 最新のニュースや情報を取得

**使用例:**
```bash
/project:gemini-search "日本のニュース"
/project:gemini-search "日本のITニュース"
/project:gemini-search "最新の技術トレンド"
```

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
* [Document](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
* [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-cod)