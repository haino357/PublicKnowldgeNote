# VSCode設定ファイルの種類の違い

VS Code の設定は「どこまで適用されるか（スコープ）」で使い分ける。

## 主な種類
- User Settings
  - 例: `~/Library/Application Support/Code/User/settings.json`（macOS）
  - その PC の全プロジェクトに適用される個人設定
- Workspace Settings
  - 例: `<project>/.vscode/settings.json`
  - そのプロジェクトだけに適用される設定（Git 共有向き）
- `.code-workspace`
  - 複数フォルダをまとめるワークスペース設定ファイル
- Folder Settings（マルチルート時）
  - 各フォルダ配下の `.vscode/settings.json`
  - フォルダ単位で設定できる
- Language-specific settings
  - `"[markdown]": { ... }` のように言語別で上書き
- Remote Settings（SSH/WSL/Dev Container）
  - リモート環境側に保存される設定

## 優先順位（上ほど優先）
1. 言語別設定（`[python]` など）
2. Workspace / Folder / `.code-workspace`
3. User Settings
4. Default Settings（VS Code 標準）

## 使い分けの目安
- 個人の好みは User Settings に入れる
- チームで揃える設定は Workspace Settings（`.vscode/settings.json`）で管理する
- 複数プロジェクトを1つの作業単位で扱う場合は `.code-workspace` を使う

## 設定ファイルの開き方
- User Settings（JSON）
  - `Cmd + Shift + P` を押す
  - `Preferences: Open User Settings (JSON)` を実行する
- Workspace Settings（JSON）
  - 対象プロジェクトを開いた状態で `Cmd + Shift + P` を押す
  - `Preferences: Open Workspace Settings (JSON)` を実行する
- `.code-workspace`
  - `.code-workspace` を開いている状態で `Cmd + Shift + P` を押す
  - `Workspaces: Open Workspace Configuration File` を実行する
- フォルダ直下から直接開く
  - `.vscode/settings.json` をエクスプローラーから開く

## あなたの環境での対応例
- ユーザー設定: `/Users/ユーザー名/Library/Application Support/Code/User/settings.json`
- プロジェクト設定: `.vscode/settings.json`
