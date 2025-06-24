---
allowed_tools: Bash(git:*), Bash(npm:*), Read(*), Fetch(*),Edit(*),WebFetch(*)
description: "引数で指定したタスクを実行します。"
---

# タスク実行コマンド

このコマンドは、指定されたフェーズに基づいてタスクを実行します。

## 使用方法
```
/project:execute-task <phase> <task_description>
```

## 実行内容

引数: $ARGUMENTS

指定されたフェーズ（explore, plan, code, document, commit, cleanup）に応じて、以下のタスクを実行してください：

### explore フェーズの場合：
1. タスクに関連するファイルを検索（Glob、Grep使用）
2. 関連するコードパターンを分析
3. 既存の実装を調査

### plan フェーズの場合：
1. `.work`ディレクトリに設計ドキュメントを作成
2. タスクの実装計画を策定
3. 必要な変更点をリストアップ

### code フェーズの場合：
1. TDD方式でテストを先に作成
2. 実装を行う
3. cargo test / cargo clippyを実行

### document フェーズの場合：
1. 関連するドキュメントを更新
2. READMEやCLAUDE.mdの必要な箇所を修正

### commit フェーズの場合：
1. git statusで変更を確認
2. 適切なコミットメッセージでコミット

### cleanup フェーズの場合：
1. `.work`ディレクトリの古いファイルを削除
2. 作業状況をサマリー

フェーズが指定されない場合は、explore → plan → code → cleanupの順で主要なフェーズを実行してください。