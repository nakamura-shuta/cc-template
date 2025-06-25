# Claude Code Template

Claude Code統合用のテンプレートリポジトリです。効率的な開発ワークフローとカスタムコマンドを提供します。

## 概要

このリポジトリは、Claude Code（claude.ai/code）を使用した開発を効率化するためのテンプレートです。プロジェクト固有のカスタムコマンドやワークフローの定義により、一貫性のある開発体験を提供します。

## 主な機能

- **カスタムコマンド**: プロジェクト固有のスラッシュコマンドで作業を自動化
- **構造化されたワークフロー**: Explore → Plan → Code → Document → Commit の開発サイクル
- **タスクテンプレート**: 一貫性のあるタスク管理とドキュメント作成
- **TDD対応**: テスト駆動開発をサポートする実装フロー

## ディレクトリ構造

```
cc-template/
├── .claude/                    # Claude Code設定
│   ├── commands/              # カスタムスラッシュコマンド
│   │   ├── commit.md         # コミット作成コマンド
│   │   ├── dump.md           # セッション履歴出力
│   │   ├── execute-task.md   # タスク実行コマンド
│   │   ├── refactor-analysis.md # リファクタリング分析
│   │   └── test.md           # テストコマンド
│   └── settings.local.json    # 権限設定
├── .work/                     # 作業ディレクトリ（gitignore対象）
├── template/                  # テンプレートファイル
│   ├── issue.md              # イシューテンプレート
│   └── task.md               # タスク指示書テンプレート
├── docs/                      # プロジェクトドキュメント
├── CLAUDE.md                  # Claude Code向け指示書
└── README.md                  # このファイル
```

## クイックスタート

### 1. リポジトリのクローン
```bash
git clone <repository-url>
cd cc-template
```

### 2. Claude Codeで開く
```bash
claude .
```

### 3. タスクの実行
```bash
# タスクテンプレートをコピーして編集
cp template/task.md my-task.md
# エディタで編集後、実行
/project:execute-task "my-task.md"
```

## カスタムコマンド

### `/project:execute-task`
タスクを段階的に実行します。
- **explore**: コードベース調査
- **plan**: 設計・計画
- **code**: TDD実装
- **document**: ドキュメント更新
- **commit**: 変更のコミット
- **cleanup**: 作業ファイルの整理

### `/project:commit`
Conventional Commit形式（絵文字付き）でコミットを作成します。

### `/project:dump`
現在のセッション履歴をログファイルとして保存します。

### `/project:refactor-analysis`
コードベースを分析し、リファクタリング計画を作成します。

## 開発ワークフロー

1. **Explore**: 関連コードの調査と理解
2. **Plan**: タスクの設計と計画（#ultrathink使用）
3. **Code**: TDD方式での実装
4. **Document**: ドキュメントの作成・更新
5. **Commit**: 変更のコミット
6. **Cleanup**: 作業ファイルの整理

## カスタマイズ

### 新しいコマンドの追加
`.claude/commands/`ディレクトリに新しい`.md`ファイルを作成します。
ファイル名がコマンド名になります（例: `analyze.md` → `/project:analyze`）

### 権限設定
`.claude/settings.local.json`でBashコマンドの実行権限を設定できます。

## 参考資料

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Awesome Claude Code](https://github.com/hesreallyhim/awesome-claude-code)

## ライセンス

このテンプレートは自由に使用・改変できます。