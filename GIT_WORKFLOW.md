# Git ワークフロー管理ガイド

## リポジトリ構成

- **メインリポジトリ**: `handydigital-dev/sniffly` (組織の公式リポジトリ)
- **個人リポジトリ**: `sh0ma360215/sniffly_win` (個人開発用フォーク)

## 推奨されるワークフロー

### 1. 初期セットアップ

```bash
# 現在のリポジトリに複数のリモートを設定
git remote add upstream https://github.com/handydigital-dev/sniffly.git
git remote add origin git@github.com:sh0ma360215/sniffly_win.git

# リモートの確認
git remote -v
# origin    git@github.com:sh0ma360215/sniffly_win.git (fetch/push)
# upstream  https://github.com/handydigital-dev/sniffly.git (fetch/push)
```

### 2. 日常的な開発フロー

#### A. メインリポジトリから最新を取得

```bash
# メインリポジトリから最新の変更を取得
git fetch upstream

# ローカルのmainブランチを更新
git checkout main
git merge upstream/main

# 個人リポジトリにも反映
git push origin main
```

#### B. 機能開発

```bash
# 新機能用のブランチを作成
git checkout -b feature/new-feature

# 開発作業...
git add .
git commit -m "feat: 新機能の実装"

# 個人リポジトリにプッシュ
git push origin feature/new-feature
```

#### C. プルリクエストの作成

1. 個人リポジトリ（sh0ma360215/sniffly_win）から組織リポジトリ（handydigital-dev/sniffly）へPRを作成
2. レビュー後、メインリポジトリにマージ

### 3. ブランチ戦略

```
handydigital-dev/sniffly (upstream)
├── main (安定版)
├── develop (開発版)
└── release/* (リリース準備)

sh0ma360215/sniffly_win (origin)
├── main (upstreamと同期)
├── feature/* (新機能開発)
├── bugfix/* (バグ修正)
└── experiment/* (実験的な変更)
```

### 4. 便利なGitエイリアス設定

```bash
# ~/.gitconfig に追加
[alias]
    # upstream から最新を取得してマージ
    sync = !git fetch upstream && git checkout main && git merge upstream/main
    
    # 現在のブランチを origin にプッシュ
    po = push origin HEAD
    
    # upstream の状態を確認
    sup = !git fetch upstream && git log HEAD..upstream/main --oneline
    
    # PR用のブランチを作成
    feature = checkout -b feature/
```

### 5. 実践的なシナリオ

#### シナリオ1: 緊急のバグ修正

```bash
# upstream から最新を取得
git sync

# hotfixブランチを作成
git checkout -b hotfix/critical-bug

# 修正を実施
git add .
git commit -m "fix: 重要なバグを修正"

# 個人リポジトリにプッシュ
git push origin hotfix/critical-bug

# GitHub上でPRを作成 (sh0ma360215/sniffly_win → handydigital-dev/sniffly)
```

#### シナリオ2: 長期的な機能開発

```bash
# 機能ブランチで開発中
git checkout feature/big-feature

# 定期的にupstreamの変更を取り込む
git fetch upstream
git merge upstream/main

# コンフリクトがある場合は解決
git add .
git commit -m "merge: upstream/main の変更を取り込み"

# 個人リポジトリに保存
git push origin feature/big-feature
```

### 6. セキュリティとアクセス管理

#### 複数アカウントの管理

```bash
# SSHの設定 (~/.ssh/config)
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal

Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work

# リモートURLの設定
git remote set-url origin git@github-personal:sh0ma360215/sniffly_win.git
git remote set-url upstream git@github-work:handydigital-dev/sniffly.git
```

### 7. トラブルシューティング

#### 認証エラーの解決

```bash
# GitHub CLIを使用した認証
gh auth login

# 特定のリモートに対して認証情報をリセット
git remote set-url origin https://sh0ma360215@github.com/sh0ma360215/sniffly_win.git
```

#### マージコンフリクトの解決

```bash
# upstream の変更を取得
git fetch upstream

# マージを試みる
git merge upstream/main

# コンフリクトを解決後
git add .
git commit -m "resolve: upstream とのコンフリクトを解決"
```

### 8. ベストプラクティス

1. **定期的な同期**: 最低でも週1回は upstream から最新を取得
2. **小さなコミット**: 機能単位で細かくコミット
3. **明確なコミットメッセージ**: conventional commits形式を使用
4. **ブランチの整理**: マージ済みのブランチは削除
5. **PRテンプレート**: 組織リポジトリにPRテンプレートを用意

### 9. 自動化スクリプト

```bash
#!/bin/bash
# sync-repos.sh - リポジトリを同期するスクリプト

echo "🔄 Syncing with upstream..."
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

echo "🧹 Cleaning up merged branches..."
git branch --merged | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d

echo "✅ Sync complete!"
```

## まとめ

このワークフローにより：
- 組織のコードベースを常に最新に保つ
- 個人の実験的な変更を安全に管理
- スムーズなコラボレーションを実現
- コードの品質を維持

重要なのは、upstream（組織）とorigin（個人）の役割を明確に分け、適切なタイミングで同期を取ることです。