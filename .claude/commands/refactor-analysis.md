---
allowed_tools: Bash(git:*), Bash(npm:*), Read(*), Fetch(*),Edit(*),WebFetch(*)
description: "現在のコードを分析してリファクタリング計画を作成します。"
---

# リファクタリング分析コマンド

現在のコードベースを詳細に分析し、以下の観点からリファクタリング計画を立案してください。計画は@docs/refactor.mdに記述してください。

## 分析項目

1. **コード品質の評価**
   - 現在のアーキテクチャパターンの遵守状況
   - コードの重複や冗長性
   - 責務の分離と単一責任原則の遵守
   - エラーハンドリングの一貫性

2. **パフォーマンスの観点**
   - 不要なクローンやコピー
   - 非効率な処理やアルゴリズム
   - 並行処理の最適化可能性

3. **保守性の向上**
   - テストカバレッジの改善点
   - ドキュメンテーションの不足箇所
   - 命名の一貫性と明確性
   - モジュール構造の改善

4. **セキュリティの観点**
   - 脆弱性の可能性
   - 入力検証の不足
   - 秘密情報の取り扱い

5. **技術的負債**
   - 古いパターンや非推奨の実装
   - TODOコメントの処理
   - 依存関係の更新必要性

## 出力形式

@docs/refactor.mdに以下の形式で記述：

```markdown
# リファクタリング計画

## 分析日時
YYYY-MM-DD

## 現状分析
### 良い点
- ...

### 改善点
- ...

## リファクタリング項目

### 優先度: 高
1. **項目名**
   - 現状: ...
   - 改善案: ...
   - 影響範囲: ...
   - 実装工数: ...

### 優先度: 中
...

### 優先度: 低
...

## 実装順序の推奨
1. ...
2. ...

## 注意事項
- ...
```

$ARGUMENTS