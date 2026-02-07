# VSCode設定の同期範囲

## 結論
- GitHub アカウントで VS Code にサインインし、Settings Sync を有効化すると **ユーザー設定（User Settings）** は同期できる。
- **ワークスペース設定（.vscode/settings.json / .code-workspace）** は基本的に Settings Sync の対象外。

## ユーザー設定（同期される）
- `User settings`（ユーザーの `settings.json`）
- 拡張機能
- キーバインド など

## ワークスペース設定（同期されない）
- `.vscode/settings.json`
- `.code-workspace`

## ワークスペース設定を共有する方法
1. プロジェクト内の `.vscode/settings.json` を Git で管理する
2. `.code-workspace` ファイルをチームで共有する

## 参考
- https://code.visualstudio.com/docs/editor/settings-sync
- https://code.visualstudio.com/docs/configure/settings-sync
- https://code.visualstudio.com/docs/configure/settings
- https://code.visualstudio.com/docs/editing/workspaces/workspaces
