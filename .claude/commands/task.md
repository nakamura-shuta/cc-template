---
allowed_tools: Bash(git:*), Bash(npm:*), Read(*), Fetch(*),Edit(*),WebFetch(*)
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

### explore フェーズの場合：
1. タスクに関連するファイルを検索（Glob、Grep使用）
2. 関連するコードパターンを分析
3. 既存の実装を調査

### plan フェーズの場合：
1. Claude CodeのSession IDを取得（可能な場合）
2. `.work/sessions/${SESSION_ID}/`ディレクトリに設計ドキュメントを作成
3. タスクの実装計画を策定
4. 必要な変更点をリストアップ

- planフェーズは必ず #ultrathink で実施してください
- 計画を作成する場合、具体的な工数日時を記述する必要はありません

### code フェーズの場合：
1. 現在のセッションの作業ディレクトリを確認
2. TDD方式でテストを先に作成
3. 実装を行う
4. (Rustの場合)cargo test / cargo clippyを実行
   - プロジェクトに応じてlintやformatを実施してください。

### document フェーズの場合：
1. 関連するドキュメントを更新
2. READMEやCLAUDE.mdの必要な箇所を修正

### commit フェーズの場合：
1. git statusで変更を確認
2. 適切なコミットメッセージでコミット

### cleanup フェーズの場合：
1. `.work/sessions/`ディレクトリの古いセッションファイルを削除（7日以上前）
2. `.work/sandbox`の不要なファイルを削除
3. 現在のセッションの作業状況をサマリー

フェーズが指定されない場合は、explore → plan → code → cleanupの順で主要なフェーズを実行してください。

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