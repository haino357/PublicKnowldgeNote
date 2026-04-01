# git worktree ガイド

`git worktree` は、**1つのリポジトリから複数の作業ディレクトリ（ワークツリー）を同時に作成できる** Git の機能。

## 基本概念

通常、1つのリポジトリにつき1つの作業ディレクトリしかないが、`git worktree` を使うと **別ブランチを別ディレクトリで同時に開いて作業** できる。

## 主なコマンド

```bash
# 新しいワークツリーを作成（既存ブランチ）
git worktree add ../path/to/new-dir branch-name

# 新しいブランチを作りつつワークツリーを作成
git worktree add -b new-branch ../path/to/new-dir

# ワークツリー一覧を表示
git worktree list

# 不要になったワークツリーを削除
git worktree remove ../path/to/new-dir

# 削除済みワークツリーの参照をクリーンアップ
git worktree prune
```

## 使いどころ

| シーン | 説明 |
|--------|------|
| **緊急のバグ修正** | 作業中のブランチを stash せずに、別ディレクトリで hotfix ブランチを開ける |
| **PR レビュー** | レビュー対象ブランチを別ディレクトリでチェックアウトして動作確認 |
| **ビルド比較** | main と feature ブランチを同時にビルドして比較 |
| **長時間ビルド中の別作業** | ビルド中のディレクトリに触らず、別ブランチで開発を続行 |

## `git stash` / `git switch` との違い

- **`git stash` + `git switch`**: 1つのディレクトリで切り替え。作業状態の保存・復元が必要
- **`git worktree`**: 複数ディレクトリが同時に存在。切り替え不要で並行作業可能

## 注意点

- **同じブランチを複数のワークツリーでチェックアウトできない**（衝突防止）
- ワークツリーは `.git` ファイル（ディレクトリではなくファイル）で本体リポジトリを参照する仕組み
- 使い終わったら `git worktree remove` で片付ける（放置するとディスクを消費）

## 具体例

```bash
# 今 feature-a ブランチで作業中だが、緊急で main の hotfix が必要
git worktree add ../WorkTaskBoard-hotfix main

# hotfix ディレクトリで修正・コミット・プッシュ
cd ../WorkTaskBoard-hotfix
# ... 修正作業 ...

# 終わったら削除
cd ../WorkTaskBoard
git worktree remove ../WorkTaskBoard-hotfix
```
