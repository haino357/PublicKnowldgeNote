# PublicKnowldgeNote

個人の技術ノート、公開記事、再利用できる知識、Webコンテンツをまとめた公開ナレッジベースです。

## 🔗 主な入口

- [GitHub Pages 公開トップ](https://haino357.github.io/PublicKnowldgeNote/)
- [初心者向けカメラ選びガイド](https://haino357.github.io/PublicKnowldgeNote/camera-guide/)
- [記事ダッシュボード](Articel/_Article%20DashBoard.md)
- [記事管理ルール](Articel/README.md)

## 📂 ディレクトリ構成

| ディレクトリ | 役割 |
|---|---|
| [`AiCodeAssistant`](AiCodeAssistant/) | AIコードアシスタントの使い方・設定 |
| [`Articel`](Articel/) | アイデアから公開済みまでの記事原稿。進捗は`status`で管理 |
| [`Develop`](Develop/) | 設計原則、CI/CD、開発ノウハウ、テンプレート |
| [`Flutter`](Flutter/) | Flutterの環境設定、Lint、アーキテクチャガイド |
| [`Git`](Git/) | Gitの操作・運用ガイド |
| [`MacRelatedSettings`](MacRelatedSettings/) | macOS、ターミナル、SSH、シェルの設定メモ |
| [`mobile`](mobile/) | モバイル開発・エミュレータ関連 |
| [`Template`](Template/) | 再利用する文書テンプレート |
| [`VSCode`](VSCode/) | VS Codeの設定・同期・Tips |
| [`docs`](docs/) | GitHub Pagesで公開する静的HTML |

## 🌐 GitHub Pages

`main`ブランチの`/docs`を公開ルートとして、静的HTMLをGitHub Pagesで配信しています。

```text
docs/
├── .nojekyll
├── index.html
└── camera-guide/
    └── index.html
```

新しいWebコンテンツは`docs/<page-slug>/index.html`として追加し、`docs/index.html`からリンクします。

## 📝 Articel

記事の進捗は、フォルダ分けではなくFrontmatterの`status`で管理します。詳細は[記事管理ルール](Articel/README.md)を参照してください。

### 最近の公開記事

- [働いた時間は何に変わったか——「5つの資」でキャリアを棚卸しする](Articel/働いた時間は何に変わったか——「5つの資」でキャリアを棚卸しする.md)
- [言語化が難しすぎる——センスではなく手順で解決する4行フォーマット](Articel/言語化が難しすぎる——センスではなく手順で解決する4行フォーマット.md)
- [Obsidianのノート管理は「フォルダ中心」から「Dashboard中心」へ](Articel/Obsidianのノート管理は「フォルダ中心」から「Dashboard中心」へ.md)
- [Claudeは情報を盛って主張を薄める〜何を書かないかは、人間の仕事〜](Articel/Claudeは情報を盛って主張を薄める〜何を書かないかは、人間の仕事〜.md)
- [「できない」を「まだできていない」に思考を変える](Articel/「できない」を「まだできていない」に思考を変える.md)
- [AIとObsidianをデータ量産装置にしないために](Articel/AIとObsidianをデータ量産装置にしないために.md)
- [インターネットは「便利な技術」を飛び越えて「文明」になっている](Articel/インターネットは「便利な技術」を飛び越えて「文明」になっている.md)
- [Obsidianで読書管理DashBoardを作った話](Articel/Obsidianで読書管理DashBoardを作った話.md)
- [Obsidianは「保管庫」ではなく「変換装置」として使う](Articel/Obsidianは「保管庫」ではなく「変換装置」として使う.md)
- [記録をとりながら作業することは大切だと思うがなかなか続かない](Articel/記録をとりながら作業することは大切だと思うがなかなか続かない.md)

## 🧰 技術ノート

### 🤖 AiCodeAssistant

- [GitHub Copilot](AiCodeAssistant/GitHubCopilot/GitHub%20Copilot.md)
- [copilot-instructions](AiCodeAssistant/GitHubCopilot/copilot-instructions.md)

### 🛠 Develop

- [SOLID原則](Develop/SOLID原則.md)
- [DRY原則](Develop/DRY原則.md)
- [CI/CD](Develop/CI_CD.md)
- [PRテンプレート](Develop/PRテンプレート.md)
- [開発ノウハウ](Develop/開発ノウハウ.md)

### 📱 Flutter

- [Flutter環境設定](Flutter/00.Flutter環境設定.md)
- [Flutter lint](Flutter/001.Flutter%20lint.md)
- [CleanArchitecture + Riverpod + MVVM 初期開発ガイド](Flutter/CleanArchitecture_Riverpod_MVVM_初期開発ガイド/00.CleanArchitecture_Riverpod_MVVM_初期開発ガイドINDEX.md)

### 🌿 Git

- [git worktreeガイド](Git/git-worktree-guide.md)

### 🍎 MacRelatedSettings

- [Macショートカットキー](MacRelatedSettings/Macショートカットキー.md)
- [SSH](MacRelatedSettings/SSH.md)
- [Shell](MacRelatedSettings/Shell.md)
- [ターミナル](MacRelatedSettings/ターミナル.md)

### 📲 mobile

- [エミュレータ](mobile/エミュレータ.md)

### 📋 Template

- [アジェンダテンプレート](Template/アジェンダテンプレート.md)

### 💻 VSCode

- [INDEX](VSCode/INDEX.md)
- [VSCode設定の同期範囲](VSCode/VSCode設定の同期範囲.md)
- [VSCode設定ファイルの種類の違い](VSCode/VSCode設定ファイルの種類の違い.md)
- [現在のVSCodeユーザー設定](VSCode/現在のVSCodeユーザー設定.md)

## 運用方針

- 公開可能な情報だけを保存し、認証情報や非公開の業務情報は含めない
- 記事の進捗は`Articel`内のFrontmatterにある`status`で管理する
- GitHub Pagesへ公開するHTMLは`docs`へ集約する
- 外部情報を利用したノートには、確認できる出典やリンクを残す
- READMEには代表的な入口を掲載し、詳細な一覧は各ディレクトリで管理する
