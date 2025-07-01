---
allowed_tools: Bash(git:*), Bash(npm:*), Read(*), Write(*), Glob(*), Edit(*), WebFetch(*)

description: "引数で指定したタスクを実行します。"
---



# タスク実行コマンド

このコマンドは、指定されたフェーズに基づいてタスクを実行します。Claude CodeのSession IDを使用して作業ファイルを管理します。

## 使用方法
```
/project:task <phase> <task_description>
```

## 実行内容

引数: $ARGUMENTS

Claude CodeのSession IDを使用して、`.work`ディレクトリ内に作業ファイルを管理します。Session IDは環境変数やコンテキストから取得を試み、取得できない場合はタイムスタンプベースのIDを使用します。

指定されたフェーズ（explore, plan, code, document, commit, cleanup）に応じて、以下のタスクを実行してください：

フェーズが指定されない場合は、explore → plan → code → cleanupの順ですべてのフェーズを実行してください。
これらのタスク,フェーズを開始するときは
開始の宣言をしてから実施してください。

### explore フェーズの場合：

1. タスクに関連するファイルを検索（Glob、Grepなどを使用）
2. 関連するコードパターンを分析
3. 既存の実装を調査
4. **依存関係の洗い出し（パッケージ、モジュール、外部API等）**
5. **潜在的な影響範囲の特定**
6. **類似機能の実装パターンを収集**
7. **技術的制約や前提条件の確認**

**成果物**: `.work/sessions/${SESSION_ID}/exploration_report.md`に調査結果をまとめる
- 関連ファイル一覧
- 既存実装の概要
- 依存関係マップ
- 推奨アプローチ

### plan フェーズの場合：
1. Claude CodeのSession IDを取得（可能な場合）
2. `.work/sessions/${SESSION_ID}/`ディレクトリに内部用設計ドキュメントを作成
3. タスクの実装計画を策定
4. 必要な変更点をリストアップ
5. **テスト戦略の策定（単体テスト、統合テスト）**
6. **リスク評価と対策の検討**
7. **実装の優先順位付け**
8. **ブレークダウンされたサブタスクの定義**

- planフェーズは必ず #ultrathink で実施してください
- 計画を作成する場合、具体的な工数日時を記述する必要はありません
- **実装の順序と依存関係を明確にする**
- **各サブタスクの完了条件を定義する**

**成果物**: `.work/sessions/${SESSION_ID}/implementation_plan.md`

### code フェーズの場合：
1. 現在のセッションの作業ディレクトリを確認
2. **planフェーズの成果物を参照し、実装順序を確認**
3. TDD方式でテストを先に作成
4. 実装を行う
5. **エラーハンドリングとログ出力の実装**
6. **コードレビューチェックリストに基づく自己レビュー**
7. プロジェクトに応じてlintやformatを実施してください
   - **Rustの場合** cargo test , cargo clippy
   - **TypeScriptの場合: npm test, npm run lint**
   - **Pythonの場合: pytest, flake8/black**
   - その他プロジェクトできまっている方法があるなら、それを優先する
8. 作成したテストがパスすることを確認する
9. **パフォーマンステストの実施（必要に応じて）**
10. **セキュリティチェック（依存関係の脆弱性確認）**

**成果物**: 
- 実装コード
- テストコード
- `.work/sessions/${SESSION_ID}/code_review_checklist.md`

### document フェーズの場合：
1. @docs以下に、関連するドキュメントを作成または更新
2. READMEやCLAUDE.mdの必要な箇所を修正
3. **APIドキュメントの生成/更新（該当する場合）**
4. **変更履歴（CHANGELOG）の更新**
5. **コード内のコメントとドキュメント文字列の確認**
6. **使用例やサンプルコードの追加**
7. **トラブルシューティングガイドの更新**

- マークダウン形式で記述する
- 簡潔でわかりやすい文章で記述する
- コードの記述は必要な部分だけ、最小限で記述する
- **図表やダイアグラムの追加も検討（Mermaid形式推奨）**
- **ドキュメントの一貫性を保つ（用語、スタイル）**

**成果物**:
- 更新されたドキュメント
- `.work/sessions/${SESSION_ID}/documentation_changes.md`（変更内容のサマリー）

### commit フェーズの場合：

1. git statusで変更を確認
2. **git diffで変更内容を詳細確認**
3. **関連ファイルをグループ化してステージング**
4. **コミットメッセージの作成（Conventional Commits形式推奨）**
   - type(scope): subject
   - 例: feat(auth): add OAuth2 integration
5. **プレコミットフックの実行確認（設定されている場合）**
6. 適切なコミットメッセージでコミット
7. **コミット後の確認（git log --oneline -n 5）**

**コミットタイプ**:
- feature: 新機能
- fix: バグ修正
- docs: ドキュメントのみの変更
- style: コードの意味に影響しない変更
- refactor: バグ修正や機能追加を伴わないコード変更
- test: テストの追加や修正
- chore: ビルドプロセスやツールの変更やその他小さな修正

**成果物**: コミットハッシュを`.work/sessions/${SESSION_ID}/commit_log.md`に記録


### cleanup フェーズの場合：
1. `.work/sessions/`ディレクトリの古いセッションファイルを削除（7日以上前）
2. `.work/sandbox`の不要なファイルを削除
3. 現在のセッションの作業状況をサマリー
4. **未完了タスクの洗い出しと記録**
5. **次回作業時の注意点や申し送り事項の記録**
6. **使用したリソースの解放（一時ファイル、ロック等）**
7. **作業統計の集計（変更行数、テストカバレッジ等）**
8. **改善提案の記録**

**成果物**: 
- `.work/sessions/${SESSION_ID}/session_summary.md`
  - 完了したタスク
  - 未完了タスク
  - 学んだこと
  - 改善提案
  - 次回への申し送り
- `.work/sessions/${SESSION_ID}/metrics.json`（作業統計）

## Session ID管理

作業ファイルは以下の構造で管理します：
```
.work/
└── sessions/
    ├── claude_session_abc123/     # Claude CodeのSession ID
    │   ├── task.md
    │   └── notes.md
    └── 20250624_231232/           # フォールバック（タイムスタンプ）
        └── task.md
```

Session IDの取得優先順位：
1. Claude Code環境変数から取得
2. 現在のコンテキストから推定
3. タイムスタンプベースのID生成（フォールバック）

## 実行前チェックリスト

各フェーズ実行前に以下を確認：
1. ✅ 作業ディレクトリの存在確認
2. ✅ 必要な権限の確認
3. ✅ 前段階の成果物の存在確認
4. ✅ 外部依存関係の状態確認
5. ✅ git リポジトリの状態確認（未コミット変更等）

## ログ出力形式

```
[2024-01-15 10:30:15] [INFO] [explore] Starting file search...
[2024-01-15 10:30:16] [DEBUG] [explore] Found 23 relevant files
[2024-01-15 10:30:17] [WARN] [explore] Dependency conflict detected
[2024-01-15 10:30:18] [ERROR] [explore] File access denied: /restricted/file
```
