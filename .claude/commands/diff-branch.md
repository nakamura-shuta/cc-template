---
allowed_tools: Bash(git:*), Read(*), Task(*), TodoWrite(*), TodoRead()
description: "ブランチ間の変更点を検出・説明し、オプションでコードレビューを実施"
---

# ブランチ差分解析コマンド

このコマンドは、現在のブランチと指定したブランチの間の変更点を検出し、わかりやすく説明します。
`--review`オプションを付けることで、変更に対するコードレビューも実施します。

## 使用方法
```
/project:diff-branch <target-branch> [--review]
```

## 引数
- `<target-branch>`: 比較対象のブランチ名（例: main, develop, feature/xxx）
- `--review`: オプション。指定すると変更に対するコードレビューを実施

## 実行内容

引数: $ARGUMENTS

### 処理フロー

1. **現在のブランチ確認**
   - 現在のブランチ名を取得
   - 作業ツリーの状態を確認

2. **差分の取得**
   - 指定されたブランチとの差分を取得
   - 変更されたファイルのリストを作成
   - 各ファイルの変更内容を詳細に取得

3. **変更内容の分析**
   - 変更の種類（追加/修正/削除）を分類
   - 変更の規模と影響範囲を評価
   - 主要な変更点を要約

4. **レポート作成**
   - 変更サマリーの生成
   - ファイルごとの変更説明
   - 統計情報（変更行数、ファイル数など）

5. **コードレビュー（--reviewオプション時）**
   - コード品質の評価
   - 潜在的な問題の指摘
   - 改善提案
   - セキュリティ上の懸念事項
   - パフォーマンスへの影響

### 出力形式

#### 基本出力
```markdown
## 📊 ブランチ差分レポート

**比較元**: current-branch
**比較先**: target-branch
**変更ファイル数**: X files
**追加行数**: +XXX lines
**削除行数**: -XXX lines

### 📝 変更サマリー
[主要な変更点の概要]

### 📁 変更ファイル一覧
[ファイルごとの変更内容]
```

#### レビュー出力（--review時）
```markdown
## 🔍 コードレビュー

### ✅ 良い点
[評価できる実装内容]

### ⚠️ 改善提案
[改善が望ましい箇所]

### 🚨 要注意事項
[修正が必要な問題]

### 💡 推奨事項
[より良い実装のための提案]
```

## 使用例

```bash
# mainブランチとの差分を確認
/project:diff-branch main

# developブランチとの差分を確認してレビュー
/project:diff-branch develop --review

# 特定の機能ブランチとの差分を確認
/project:diff-branch feature/authentication
```

## 注意事項

- コミットされていない変更がある場合は警告を表示
- 存在しないブランチを指定した場合はエラーを表示
- 大量の変更がある場合は、主要な変更点に焦点を当てて説明
- レビューは建設的で実用的な提案を心がける