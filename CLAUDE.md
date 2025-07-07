# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

このリポジトリは現在、Claude Code統合用の空のテンプレートプロジェクトです。
ここにプロジェクト内容を記述します。

# 共通コマンド

#### ファイル操作

- `find`, `ls`, `cat` - ファイル検索と閲覧
- `mkdir`, `mv`, `rename` - ディレクトリ作成とファイル移動

#### Git操作

- Github操作には`gh`使用を推奨

#### Rust開発（Cargoコマンド）

- `cargo test` - テスト実行
- `cargo clippy` - Lintチェック
- `cargo fmt` - コードフォーマット
- `cargo check` - コンパイルチェック
- `cargo` - その他のCargoコマンド

#### 検索

- `rg` (ripgrep), `grep` - ファイル内容検索


## コードスタイル

- 可能な限り分割代入を活用
- 関数名は snake_case、クラス名は PascalCase で統一

## ワークフロー

### 作業の進め方

1.Explore
まずはコードベースや関連ファイルなどを
理解するフェーズ。

ex.
```
> find the files that handle user authentication
```

2.Plan
実装の計画や設計について深く考るフェーズ。
ここでは#ultrathink を使用。

ex.
```
> how to implement Role Based User Authentication #ultrathink 
```

ここで設計や計画を行い、@.work/${sessionId}/task.mdを作成して(内部的、リポジトリ共有しない)設計方針や実装方針、計画などを記述する。
また、@docs以下には(リポジトリに共有される)ドキュメントを作成・更新する。

3.Code
計画に基づき、具体的なコード実装するフェーズ。
後述するTDD方式で実装していく。

ex.
```
> implement its solution
```

@.work/${sessionId}/task.mdに進捗状況を記述していく。

4.Document

@docs以下にドキュメントを作成・更新する。

5.Commit
実装された変更内容をコミットするフェーズ。

ex.
```
> commit this
```

6.cleanup

@.work/sandboxなどに作成した使用済みファイルなどをcleanupする。

### TDDで進める

1.テストを実装
まずは、実装したい機能のテストコードを実装する。
このテストは、最初は失敗するように（機能が未実装なので）実装。
実際にテストが失敗することを確認する。

2.テストをパスするように実装する
次に、テストコードを修正せずに、
そのテストがパスするように実装コードを書く。
テストを再度実行してパスすればOK.

### その他注意事項

- 変更完了後は必ず型チェックを実行
- 全テストではなく単体テストを優先して実行

## 権限設定

`.claude/settings.local.json`ファイルで以下のBashコマンドが許可されています：


## プロジェクト構造

```
cc-template/
├── .claude/
│   ├── commands/
│   │   ├── README.md          # カスタムコマンドの説明
│   │   ├── task.md             # タスク実行コマンド
│   │   └── refactor-analysis.md # リファクタリング分析コマンド
│   └── settings.local.json    # Claude権限設定
├── .work/                     # 作業用ディレクトリ（セッションごとのタスク管理）
│   └── ${sessionId}/
│       └── task.md           # タスク設計ドキュメント
├── docs/                      # プロジェクトドキュメント
└── CLAUDE.md                 # このファイル
```

## Playwright MCP使用ルール

### 絶対的な禁止事項

1. **いかなる形式のコード実行も禁止**

   - Python、JavaScript、Bash等でのブラウザ操作
   - MCPツールを調査するためのコード実行
   - subprocessやコマンド実行によるアプローチ

2. **利用可能なのはMCPツールの直接呼び出しのみ**

   - playwright:browser_navigate
   - playwright:browser_screenshot
   - 他のPlaywright MCPツール

3. **エラー時は即座に報告**
   - 回避策を探さない
   - 代替手段を実行しない
   - エラーメッセージをそのまま伝える


## その他注意事項

- 特に指定がない限り、回答は日本語で簡潔に行ってください
- 必ずカスタムスラッシュコマンド(./claude/commands以下のファイルか~/.claude/commands以下のファイル)を経由して会話をするようにしてください。
カスタムスラッシュコマンドを使わず質問や依頼を受けた場合、以下の説明文を表示し、現在使用できるカスタムスラッシュコマンド(.claude/commands)を使うように説明をしてください。

```
現在の設定では、会話や依頼ははカスタムスラッシュコマンド経由でのみ可能です。
以下のカスタムスラッシュコマンドを使用してください。
```