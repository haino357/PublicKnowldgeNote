# 現在のVSCodeユーザー設定

## 更新情報
| 項目 | 内容 |
|---|---|
| 作成日時 | 2026-02-07 14:56:27 JST |
| 最終更新日時 | 2026-02-07 14:56:27 JST |
| 取得元 | `/Users/ユーザー名/Library/Application Support/Code/User/settings.json` |
| 対象 | VS Code User Settings |

## 設定内容（JSONC）
```jsonc
{
    // ========================================
    // 補完・AI支援
    // ========================================

    // 補完候補の既定選択を先頭にする
    "editor.suggestSelection": "first",

    // IntelliCode が補完選択の既定値を自動調整した状態を保持
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",

    // インライン提案を有効化
    "editor.inlineSuggest.enabled": true,

    // Copilot の言語別有効/無効設定
    "github.copilot.enable": {
        "*": true,
        "yaml": false,
        "plaintext": false,
        "markdown": true
    },

    // Copilot の次編集提案を有効化
    "github.copilot.nextEditSuggestions.enabled": true,


    // ========================================
    // エディタ表示・編集動作
    // ========================================

    // ミニマップを非表示
    "editor.minimap.enabled": false,

    // 差分表示で行末の空白差分を無視しない
    "diffEditor.ignoreTrimWhitespace": false,

    // タブ折り返し表示を有効化
    "workbench.editor.wrapTabs": true,

    // ピン留めタブを通常タブとは別の行に表示する
    "workbench.editor.pinnedTabsOnSeparateRow": true,


    // ========================================
    // ファイル・エクスプローラー・保存
    // ========================================

    // エクスプローラーで不要な Java 関連ファイルを非表示
    "files.exclude": {
        "**/.classpath": true,
        "**/.project": true,
        "**/.settings": true,
        "**/.factorypath": true
    },

    // エクスプローラーのフォルダ圧縮表示を無効化
    "explorer.compactFolders": false,

    // フォーカス移動時に自動保存
    "files.autoSave": "onFocusChange",

    // 改行コードをLFに統一（Mac/Linux向け）
    "files.eol": "\n",


    // ========================================
    // ワークベンチ外観・レイアウト
    // ========================================

    // ファイルアイコンテーマを vscode-icons にする
    "workbench.iconTheme": "vscode-icons",

    // 色を変える(Workbench)
    "workbench.colorCustomizations": {
        "titleBar.activeBackground": "#0095ff", // タイトルバーの背景色を変える
        "titleBar.activeForeground": "#080606", // タイトルバーの文字色を変える
        "activityBar.activeBackground": "#ff0000", // アクティビティバーの背景色を変える
        // "activityBar.foreground": "#ff0000", // アクティビティバーの文字色を変える
        "statusBar.background": "#0000ff", // ステータスバーの背景色を変える
        // "statusBar.foreground": "#0000ff", // ステータスバーの文字色を変える
        // "activityBarBadge.background": "#ffff00", // アクティビティバーのバッチ背景色
        // "activityBarBadge.foreground": "#000000", // アクティビティバーのバッチ文字色
        // "tab.activeBackground": "#bce7d2",   // 表示されているタブの背景色
        // "tab.activeForeground": "#000000",   // 表示されているタブの文字色
        // "tab.inactiveBackground": "#000000", // 表示されていないタブの背景色
        // "tab.inactiveForeground": "#ffffff"  // 表示されていないタブの文字色
        // "editor.background": "#f8f8ff", // エディタの背景色
    },

    // 起動時のWelcomeタブを出さない
    "workbench.startupEditor": "none",

    // アクティビティバーを上部に配置
    "workbench.activityBar.location": "top",


    // ========================================
    // Git / GitLens
    // ========================================

    // Git の自動フェッチを有効化
    "git.autofetch": true,

    // フェッチ時にリモート削除済みブランチを自動で削除しない
    "git.pruneOnFetch": false,

    // Git 同期時の確認ダイアログを表示しない
    "git.confirmSync": false,

    // ソース管理ビューでリポジトリ一覧を常に表示する
    "scm.alwaysShowRepositories": true,

    // ソース管理ビューのフォルダ圧縮表示を無効化
    "scm.compactFolders": false,

    // ソース管理ビューの既定表示をツリーにする
    "scm.defaultViewMode": "tree",

    // 差分デコレーション用ガター幅を調整
    "scm.diffDecorationsGutterWidth": 5,

    // GitLens の Git 未検出警告を抑制
    "gitlens.advanced.messages": {
        "suppressGitMissingWarning": true
    },


    // ========================================
    // 言語・ランタイム関連
    // ========================================

    // Java 拡張のヘルプビュー初期表示を Getting Started にする
    "java.help.firstView": "gettingStarted",

    // Gradle Wrapper のチェックサム許可リスト
    "java.imports.gradle.wrapper.checksums": [
        {
            "sha256": "e2b82129ab64751fd40437007bd2f7f2afb3c6e41a9198e628650b22d5824a14",
            "allowed": true
        }
    ],

    // JSON ファイルの既定フォーマッターを VS Code 標準に固定
    "[json]": {
        "editor.defaultFormatter": "vscode.json-language-features"
    },


    // ========================================
    // Live Preview 拡張
    // ========================================

    // Live Preview の自動リフレッシュを無効化
    "livePreview.autoRefreshPreview": "Never",

    // Live Preview の外部プレビュー時にデバッグ有効化
    "livePreview.debugOnExternalPreview": true,

    // Live Preview の表示先を外部ブラウザにする
    "livePreview.openPreviewTarget": "External Browser"
}

```
