# タスク実行コマンド

このファイルはワークフローの各フェーズに対応するカスタムコマンドを定義します。

## 使用方法

```bash
/project:execute-task <phase> <task_description>
```

引数:
- phase: explore, plan, code, document, commit, cleanup のいずれか
- task_description: タスクの説明（phaseがexplore, plan, code, document, commitの場合必須）

## コマンド実行ロジック

```bash
# 引数を解析
PHASE=$(echo "$ARGUMENTS" | awk '{print $1}')
TASK_DESC=$(echo "$ARGUMENTS" | cut -d' ' -f2-)

# フェーズ名のチェック
if [[ ! "$PHASE" =~ ^(explore|plan|code|document|commit|cleanup)$ ]]; then
    # 第一引数がフェーズ名でない場合、全体をタスク内容として扱い、主要フェーズを実行
    TASK_DESC="$ARGUMENTS"
    echo "=== 自動実行モード: explore → plan → code → cleanup ==="
    echo "タスク: $TASK_DESC"
    echo ""
    
    # 1. Explore
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "=== [1/4] Explore: コードベース探索 ==="
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "検索対象: $TASK_DESC"
    
    echo -e "\n関連ファイル:"
    find . -type f -name "*.js" -o -name "*.ts" -o -name "*.rs" | grep -v node_modules | grep -v .git | head -10
    
    echo -e "\n\"$TASK_DESC\"を含むファイル:"
    rg "$TASK_DESC" --type-add 'code:*.{js,ts,rs,py}' -t code -l 2>/dev/null | head -10 || echo "該当ファイルが見つかりません"
    
    echo -e "\n詳細な検索結果:"
    rg "$TASK_DESC" -A 2 -B 2 --type-add 'code:*.{js,ts,rs,py}' -t code 2>/dev/null | head -30 || echo "該当する内容が見つかりません"
    
    # 2. Plan
    echo -e "\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "=== [2/4] Plan: 設計・計画フェーズ ==="
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    SESSION_ID=$(date +%Y%m%d_%H%M%S)
    
    mkdir -p .work/$SESSION_ID
    cat > .work/$SESSION_ID/task.md << EOF
# Task: $TASK_DESC

## 設計方針

## 実装計画

## 進捗状況
- [ ] テスト作成
- [ ] 実装
- [ ] ドキュメント更新

## セッションID: $SESSION_ID
EOF
    
    echo "タスクファイル作成完了: .work/$SESSION_ID/task.md"
    echo "セッションID: $SESSION_ID"
    
    # 3. Code
    echo -e "\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "=== [3/4] Code: TDD実装フェーズ ==="
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    if [ -f "package.json" ]; then
        echo "Node.jsプロジェクトを検出"
        echo "テストコマンド例: npm test -- --watch=false"
        echo "型チェック例: npm run typecheck"
    elif [ -f "Cargo.toml" ]; then
        echo "Rustプロジェクトを検出"
        echo "テストコマンド例: cargo test"
        echo "Lintコマンド例: cargo clippy"
    else
        echo "プロジェクトタイプが検出できません"
    fi
    
    # 4. Cleanup（簡易版）
    echo -e "\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "=== [4/4] Cleanup: 作業状況確認 ==="
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    if [ -d ".work" ]; then
        WORK_COUNT=$(find .work -type f -name "*.md" 2>/dev/null | wc -l)
        echo "現在の作業ファイル数: $WORK_COUNT"
        
        OLD_FILES=$(find .work -type f -mtime +7 2>/dev/null | wc -l)
        if [ "$OLD_FILES" -gt 0 ]; then
            echo "7日以上前の作業ファイル: $OLD_FILES 個"
        fi
    fi
    
    echo -e "\n完了！詳細な実行が必要な場合は、個別のフェーズを指定してください。"
    exit 0
fi

case "$PHASE" in
    "explore")
        echo "=== Explore: コードベース探索 ==="
        echo "検索対象: $TASK_DESC"
        
        # ファイル構造の確認
        echo -e "\n関連ファイル:"
        find . -type f -name "*.js" -o -name "*.ts" -o -name "*.rs" | grep -v node_modules | grep -v .git | head -20
        
        # 特定の機能に関連するファイルを検索
        echo -e "\n\"$TASK_DESC\"を含むファイル:"
        rg "$TASK_DESC" --type-add 'code:*.{js,ts,rs,py}' -t code -l 2>/dev/null || echo "該当ファイルが見つかりません"
        
        # ファイル内容の詳細検索
        echo -e "\n詳細な検索結果:"
        rg "$TASK_DESC" -A 3 -B 3 --type-add 'code:*.{js,ts,rs,py}' -t code 2>/dev/null | head -50 || echo "該当する内容が見つかりません"
        ;;
        
    "plan")
        echo "=== Plan: 設計・計画フェーズ ==="
        # セッションIDの生成
        SESSION_ID=$(date +%Y%m%d_%H%M%S)
        
        # タスク設計ドキュメントの作成
        mkdir -p .work/$SESSION_ID
        cat > .work/$SESSION_ID/task.md << EOF
# Task: $TASK_DESC

## 設計方針

## 実装計画

## 進捗状況
- [ ] テスト作成
- [ ] 実装
- [ ] ドキュメント更新

## セッションID: $SESSION_ID
EOF
        
        echo "タスクファイル作成完了: .work/$SESSION_ID/task.md"
        echo "セッションID: $SESSION_ID"
        ;;
        
    "code")
        echo "=== Code: TDD実装フェーズ ==="
        echo "テスト対象: $TASK_DESC"
        
        # JavaScript/TypeScriptプロジェクトの場合
        if [ -f "package.json" ]; then
            echo -e "\nNode.jsプロジェクトでテスト実行中..."
            # テスト実行
            npm test -- --watch=false $TASK_DESC 2>&1 || echo "テストが失敗しました（TDDでは期待通り）"
            
            # 型チェック
            echo -e "\n型チェック実行中..."
            npm run typecheck 2>/dev/null || npx tsc --noEmit 2>&1 || echo "型チェックコマンドが見つかりません"
        fi
        
        # Rustプロジェクトの場合
        if [ -f "Cargo.toml" ]; then
            echo -e "\nRustプロジェクトでテスト実行中..."
            # テスト実行
            cargo test $TASK_DESC 2>&1 || echo "テストが失敗しました（TDDでは期待通り）"
            
            # Lintチェック
            echo -e "\nLintチェック実行中..."
            cargo clippy -- -D warnings 2>&1
            
            # フォーマット
            echo -e "\nフォーマットチェック..."
            cargo fmt --check 2>&1
        fi
        ;;
        
    "document")
        echo "=== Document: ドキュメント更新 ==="
        echo "ドキュメント対象: $TASK_DESC"
        
        # ドキュメントディレクトリの作成
        mkdir -p docs
        
        # READMEの更新確認
        if [ -f "README.md" ]; then
            echo -e "\nREADME.mdでの関連記述:"
            rg -i "$TASK_DESC" README.md 2>/dev/null || echo "README.mdに関連する記述が見つかりません"
        fi
        
        # docsディレクトリの確認
        if [ -d "docs" ]; then
            echo -e "\ndocs/ディレクトリの内容:"
            ls -la docs/
            echo -e "\ndocs/内での関連記述:"
            rg -i "$TASK_DESC" docs/ 2>/dev/null || echo "docs/内に関連する記述が見つかりません"
        fi
        ;;
        
    "commit")
        echo "=== Commit: 変更のコミット ==="
        
        # 変更内容の確認
        echo "現在の変更状況:"
        git status --short
        
        echo -e "\n差分の確認:"
        git diff --stat
        
        # コミットメッセージが指定されている場合
        if [ -n "$TASK_DESC" ]; then
            echo -e "\nコミットメッセージ: $TASK_DESC"
            echo "この内容でコミットする場合は、以下のコマンドを実行してください:"
            echo "git add -A && git commit -m \"$TASK_DESC\""
        else
            echo -e "\nコミットメッセージを指定してください"
            echo "使用例: /project:execute-task commit \"feat: implement user authentication\""
        fi
        ;;
        
    "cleanup")
        echo "=== Cleanup: 作業ファイルのクリーンアップ ==="
        
        # 作業ディレクトリの確認
        echo "現在の作業ファイル:"
        if [ -d ".work" ]; then
            find .work -type f \( -name "*.md" -o -name "*.tmp" \) -exec ls -la {} \; 2>/dev/null | head -20
            
            # 古い作業ファイルの削除（7日以上前）
            OLD_FILES=$(find .work -type f -mtime +7 2>/dev/null | wc -l)
            if [ "$OLD_FILES" -gt 0 ]; then
                echo -e "\n7日以上前の作業ファイル数: $OLD_FILES"
                find .work -type f -mtime +7 -exec rm {} \; 2>/dev/null
                echo "古い作業ファイルを削除しました"
            else
                echo -e "\n削除対象の古いファイルはありません"
            fi
            
            # sandboxディレクトリの確認
            if [ -d ".work/sandbox" ]; then
                SANDBOX_SIZE=$(du -sh .work/sandbox 2>/dev/null | cut -f1)
                echo -e "\nsandboxディレクトリサイズ: $SANDBOX_SIZE"
                echo "クリーンアップする場合: rm -rf .work/sandbox/*"
            fi
        else
            echo "作業ディレクトリが存在しません"
        fi
        ;;
        
    *)
        echo "エラー: 不明なフェーズ '$PHASE'"
        echo ""
        echo "使用方法: /project:execute-task <phase> <task_description>"
        echo ""
        echo "利用可能なフェーズ:"
        echo "  explore  - コードベースを探索"
        echo "  plan     - タスクの設計・計画を作成"
        echo "  code     - TDD方式でコード実装"
        echo "  document - ドキュメントを更新"
        echo "  commit   - 変更をコミット"
        echo "  cleanup  - 作業ファイルをクリーンアップ"
        echo ""
        echo "例:"
        echo "  /project:execute-task explore \"user authentication\""
        echo "  /project:execute-task plan \"implement JWT authentication\""
        echo "  /project:execute-task code \"test_auth_module\""
        echo "  /project:execute-task commit \"feat: add JWT authentication\""
        echo "  /project:execute-task cleanup"
        ;;
esac
```

## 注意事項
- セッションIDはClaude Code起動時に付与されるIDです。自動生成され、自身を表すIDとして、作業ファイルの管理に使用されます
- TDDワークフローに従い、まずテストを作成してから実装を行います
- 型チェックとLintは必ず実行してください
- 作業ファイルは定期的にクリーンアップしてください