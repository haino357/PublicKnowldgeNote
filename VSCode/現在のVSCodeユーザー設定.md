# 現在のVSCodeユーザー設定

## 更新情報
| 項目 | 内容 |
|---|---|
| 作成日時 | 2026-02-07 14:56:27 JST |
| 最終更新日時 | 2026-02-10 15:24:42 JST |
| 取得元 | `/Users/ユーザー名/Library/Application Support/Code/User/settings.json` |
| 対象 | VS Code User Settings |

## 設定内容（JSONC）
```jsonc
{
    // ========================================
    // 補完・AI支援
    // ========================================

    // 補完候補の既定選択を先頭にする
    // → 毎回1番目の候補が選ばれている状態になり、Enter で確定しやすい
    "editor.suggestSelection": "first",

    // IntelliCode が補完選択の既定値を自動調整した状態を保持
    // → 学習した最適な補完候補を積極的に提示
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",

    // インライン提案を有効化
    // → コード入力時に Copilot の行内候補を表示（Ctrl/Cmd + Right で確定）
    "editor.inlineSuggest.enabled": true,

    // Copilot の言語別有効/無効設定
    // → YAML/純テキストは無効（精度低下）、Markdown では有効（文章作成に有用）
    "github.copilot.enable": {
        "*": true,           // その他すべての言語は有効
        "yaml": false,       // YAML は構文误解が多いため無効
        "plaintext": false,  // テキストファイルは無効
        "markdown": true     // Markdown は記事作成時に有用なため有効
    },

    // Copilot の次編集提案を有効化
    // → 現在の編集パターンから次の操作を予測して提案（リファクタリング効率向上）
    "github.copilot.nextEditSuggestions.enabled": true,


    // ========================================
    // エディタ表示・編集動作
    // ========================================

    // ミニマップを非表示
    // → 画面右側のミニマップ表示を無効化（スペース節約、集中力向上）
    "editor.minimap.enabled": false,

    // 差分表示で行末の空白差分を無視しない
    // → 行末スペースの増減も差分として明確に表示（コード品質管理用）
    "diffEditor.ignoreTrimWhitespace": false,

    // タブ折り返し表示を有効化
    // → タブが増えても画面内に複数行で表示されるため、すべてのタブが見える
    "workbench.editor.wrapTabs": true,

    // ピン留めタブを通常タブとは別の行に表示する
    // → 重要なファイル（ピン留め）と一時的なタブを視覚的に分離
    "workbench.editor.pinnedTabsOnSeparateRow": true,


    // ========================================
    // ファイル・エクスプローラー・保存
    // ========================================

    // エクスプローラーで不要な Java 関連ファイルを非表示
    // → Eclipse/IntelliJ 設定ファイルはプロジェクト設定であり、編集不要のため非表示化
    "files.exclude": {
        "**/.classpath": true,     // Java クラスパス設定
        "**/.project": true,       // IDE プロジェクト定義ファイル
        "**/.settings": true,      // IDE 設定ディレクトリ
        "**/.factorypath": true    // Annotation Processor 設定
    },

    // エクスプローラーのフォルダ圧縮表示を無効化
    // → 1つだけのサブフォルダを親フォルダと一行で表示しない（階層構造が明確）
    "explorer.compactFolders": false,

    // フォーカス移動時に自動保存
    // → 別のウィンドウ/エディタに移動した時点で自動保存（明示的な保存操作不要）
    "files.autoSave": "onFocusChange",

    // 改行コードをLFに統一（Mac/Linux向け）
    // → Windows との互換性維持とGit差分の混乱を防止
    "files.eol": "\n",


    // ========================================
    // ワークベンチ外観・レイアウト
    // ========================================

    // ファイルアイコンテーマを vscode-icons にする
    // → ファイル種別をアイコンで視覚的に判別（拡張機能「vscode-icons」が必須）
    "workbench.iconTheme": "vscode-icons",

    // 色をカスタマイズ（ブランド配色や視認性向上用）
    // → 複数ウィンドウを開く場合の区別や、個人設定の反映に有用
    "workbench.colorCustomizations": {
        "titleBar.activeBackground": "#0095ff",     // タイトルバーの背景色（青）
        "titleBar.activeForeground": "#080606",     // タイトルバーの文字色（濃色）
        "activityBar.activeBackground": "#ff0000",  // アクティビティバーのアクティブ色（赤）
        // "activityBar.foreground": "#ff0000",  // アクティビティバーの文字色（コメント）
        "statusBar.background": "#0000ff",          // ステータスバーの背景色（青）
        // "statusBar.foreground": "#0000ff",       // ステータスバーの文字色（コメント）
        // "activityBarBadge.background": "#ffff00", // アクティビティバーのバッチ背景色
        // "activityBarBadge.foreground": "#000000", // アクティビティバーのバッチ文字色
        // "tab.activeBackground": "#bce7d2",   // 表示されているタブの背景色
        // "tab.activeForeground": "#000000",   // 表示されているタブの文字色
        // "tab.inactiveBackground": "#000000", // 表示されていないタブの背景色
        // "tab.inactiveForeground": "#ffffff"  // 表示されていないタブの文字色
        // "editor.background": "#f8f8ff", // エディタの背景色
    },

    // 起動時のWelcomeタブを出さない
    // → 起動直後に作業を開始できる（生産性向上）
    "workbench.startupEditor": "none",

    // アクティビティバーを上部に配置（カスタマイズ例）
    // "workbench.activityBar.location": "top",    // コメント状態（デフォルトは左側）

    // アクティビティバーを縦向きで表示
    // → 画面幅を節約しつつ、すべてのアイコンが見やすい配置
    "workbench.activityBar.orientation": "vertical",


    // ========================================
    // Git / GitLens
    // ========================================

    // Git の自動フェッチを有効化
    // → リモートの最新状態を定期的に取得（ローカルリモート追跡ブランチを最新化）
    "git.autofetch": true,

    // フェッチ時にリモート削除済みブランチを自動で削除しない
    // → 誤って参照しているコードがないか確認の余地を残す（手動削除が安全）
    "git.pruneOnFetch": false,

    // Git 同期時の確認ダイアログを表示しない
    // → 頻繁な同期操作がスムーズに（pull/push が一度に実行される）
    "git.confirmSync": false,

    // ソース管理ビューでリポジトリ一覧を常に表示する
    // → マルチリポジトリ管理時に、どのリポジトリを操作中かが明確
    "scm.alwaysShowRepositories": true,

    // ソース管理ビューのフォルダ圧縮表示を無効化
    // → ファイル階層が一目でわかる（変更ファイル位置の特定が容易）
    "scm.compactFolders": false,

    // ソース管理ビューの既定表示をツリーにする
    // → 変更ファイルをフォルダ階層で整理表示（リスト表示より見やすい）
    "scm.defaultViewMode": "tree",

    // 差分デコレーション用ガター幅を調整
    // → 行左側の変更マーク（追加・変更・削除）の表示幅を制限（画面スペース節約）
    "scm.diffDecorationsGutterWidth": 5,

    // GitLens の Git 未検出警告を抑制
    // → 非Git フォルダで GitLens を使用しても警告を表示しない
    "gitlens.advanced.messages": {
        "suppressGitMissingWarning": true
    },


    // ========================================
    // 言語・ランタイム関連
    // ========================================

    // Java 拡張のヘルプビュー初期表示を Getting Started にする
    // → Java プロジェクト初期化時に役立つガイドが最初に表示される
    "java.help.firstView": "gettingStarted",

    // Gradle Wrapper のチェックサム許可リスト
    // → 特定のバージョンの Gradle に対してセキュリティ検証をスキップ
    // ⚠️  本来はセキュリティリスク - 信頼できるプロジェクトのみで使用
    "java.imports.gradle.wrapper.checksums": [
        {
            "sha256": "e2b82129ab64751fd40437007bd2f7f2afb3c6e41a9198e628650b22d5824a14",
            "allowed": true
        }
    ],

    // JSON ファイルの既定フォーマッターを VS Code 標準に固定
    // → prettier などの他のフォーマッターを優先させない（デフォルト動作を保証）
    "[json]": {
        "editor.defaultFormatter": "vscode.json-language-features"
    },


    // ========================================
    // Live Preview 拡張
    // ========================================

    // Live Preview の自動リフレッシュを無効化
    // → ファイル保存時の自動リロードで予期しない挙動を防止（明示的更新を強制）
    "livePreview.autoRefreshPreview": "Never",

    // Live Preview の外部プレビュー時にデバッグ有効化
    // → ブラウザ開発者ツール連携で、DevTools デバッグが可能になる
    "livePreview.debugOnExternalPreview": true,

    // Live Preview の表示先を外部ブラウザにする
    // → VS Code エディタ領域を圧迫しない（広いコード編集スペースを確保）
    "livePreview.openPreviewTarget": "External Browser"
}


```
