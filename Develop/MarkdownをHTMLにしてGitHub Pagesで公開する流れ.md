---
title: MarkdownをHTMLにしてGitHub Pagesで公開する流れ
aliases:
  - GitHub Pages公開フロー
  - MarkdownのHTML公開手順
created: 2026-09-04
updated: 2026-09-04
tags:
  - GitHub-Pages
  - Markdown
  - HTML
  - 公開運用
type: guide
---

# MarkdownをHTMLにしてGitHub Pagesで公開する流れ

他人に共有したい内容をMarkdownで整理し、読みやすいHTMLへ変換して、`PublicKnowldgeNote`のGitHub Pagesで公開するまでの手順をまとめる。

> [!important] 基本原則
> Markdownを内容の正本、HTMLを公開用の成果物として扱う。内容を直すときは原則としてMarkdownを先に更新し、その内容をHTMLへ反映する。

## 現在の公開構成

| 項目 | 現在の設定 |
|---|---|
| GitHubリポジトリ | [haino357/PublicKnowldgeNote](https://github.com/haino357/PublicKnowldgeNote) |
| 公開ブランチ | `main` |
| 公開ルート | `/docs` |
| 公開トップ | [https://haino357.github.io/PublicKnowldgeNote/](https://haino357.github.io/PublicKnowldgeNote/) |
| HTTPS | 有効 |

```text
PublicKnowldgeNote/
├── Articel/・Develop/など       # Markdownの保存場所
├── README.md
└── docs/                       # GitHub Pagesの公開ルート
    ├── .nojekyll
    ├── index.html              # 公開コンテンツ一覧
    └── <page-slug>/
        ├── index.html          # 各コンテンツの入口
        └── assets/             # 必要な場合のみ画像などを置く
```

## 全体の流れ

```mermaid
flowchart LR
    A[Markdownで原稿を作る] --> B[公開可否を確認]
    B --> C[英数字のslugを決める]
    C --> D[HTMLを生成する]
    D --> E[docs/slug/index.htmlへ配置]
    E --> F[docs/index.htmlへ導線を追加]
    F --> G[表示・リンク・差分を確認]
    G --> H[対象ファイルだけコミット]
    H --> I[mainへpush]
    I --> J[GitHub Pagesへ自動反映]
    J --> K[公開URLを確認・共有]
```

## 1. Markdownで共有内容を作る

共有したい内容を、用途に合うディレクトリのMarkdownとして作成する。

- 公開記事の原稿：`Articel/`
- 技術や手順のノート：`Develop/`、`Git/`、`Flutter/`など
- まだ保存先が決まっていない原稿：Vaultの`000_Inbox/`

原稿には最低限、次を含める。

- 誰に向けた内容か
- 読んだ人が得られる結論
- 手順、比較、判断基準
- 数値や製品情報の確認日
- 外部情報を使った場合の出典

> [!warning] 公開前の確認
> 個人情報、認証情報、非公開の業務情報、ローカルの絶対パスが含まれていないことを確認する。公開してよいか迷う情報はHTMLへ入れない。

## 2. 公開URL用のslugを決める

HTMLごとに`docs`直下へディレクトリを作る。ディレクトリ名はURLの一部になるため、半角英小文字・数字・ハイフンを使う。

```text
良い例
camera-guide
github-pages-guide
investment-glossary

避ける例
カメラ選び
Camera Guide
new_page
```

`camera-guide`の場合、出力先と公開URLは次の対応になる。

```text
docs/camera-guide/index.html
↓
https://haino357.github.io/PublicKnowldgeNote/camera-guide/
```

## 3. MarkdownをHTMLへ変換する

現在は、Markdownを材料としてCodexへHTML化を依頼し、CSSと必要なJavaScriptを含む静的HTMLを作る。`pandoc`は現在の環境に入っていないため、変換コマンドには依存しない。

### Codexへの依頼例

```text
このMarkdownを、他人へ共有するための読みやすいHTMLにしてください。

入力:
PublicKnowldgeNote/Develop/<原稿名>.md

出力:
PublicKnowldgeNote/docs/<page-slug>/index.html

要件:
- スマートフォンとPCの両方で読みやすくする
- CSSはHTML内へ含める
- 必要な場合だけJavaScriptを使う
- 見出し構造と出典リンクを維持する
- ローカルの絶対パスを含めない
- 外部リンクは安全に新しいタブで開く
- 公開トップのdocs/index.htmlにもリンクを追加する
```

### ファイル構成の判断

文章、CSS、JavaScriptだけなら、管理しやすいように`index.html`へまとめる。

```text
docs/<page-slug>/index.html
```

画像やダウンロードファイルがある場合は、ページ専用の`assets`へ置き、相対パスで参照する。

```text
docs/<page-slug>/
├── index.html
└── assets/
    ├── cover.webp
    └── diagram.svg
```

```html
<img src="./assets/cover.webp" alt="内容が分かる代替テキスト">
```

## 4. 公開トップへ追加する

新しいページを作ったら、`docs/index.html`の公開コンテンツ一覧へリンクを追加する。

```html
<a href="./<page-slug>/">ページタイトル</a>
```

リンク先を`index.html`まで書かず、ディレクトリを指す形に統一する。

## 5. ローカルで検証する

### 必須確認

- [ ] PC幅とスマートフォン幅でレイアウトが崩れていない
- [ ] 見出し、表、箇条書きが読みやすい
- [ ] 公開トップから新しいページを開ける
- [ ] ページ内リンクと外部リンクが正しい
- [ ] 画像とCSSが相対パスで読み込まれる
- [ ] 個人情報、認証情報、ローカル絶対パスがない
- [ ] 原稿MarkdownとHTMLの内容が食い違っていない

### HTMLとJavaScriptの確認例

```bash
tidy -errors -quiet "docs/<page-slug>/index.html"
```

JavaScriptをHTML内に記述した場合は、スクリプト部分の構文も確認する。

```bash
sed -n '/<script>/,/<\/script>/p' "docs/<page-slug>/index.html" \
  | sed '1d;$d' \
  | node --check
```

> [!note]
> `tidy`はHTML5の装飾用空要素などを警告する場合がある。警告だけで判断せず、ブラウザ表示と実際の操作も確認する。

## 6. 公開対象だけをGitへ追加する

`PublicKnowldgeNote`リポジトリへ移動し、最初に差分を確認する。

```bash
cd PublicKnowldgeNote
git status --short
```

新しい公開ページとトップページだけを指定してステージする。

```bash
git add "docs/<page-slug>/index.html" "docs/index.html"
```

画像がある場合は、ページディレクトリ単位で追加する。

```bash
git add "docs/<page-slug>" "docs/index.html"
```

ステージ後に、関係のないファイルが混ざっていないことを確認する。

```bash
git diff --cached --stat
git diff --cached --check
git diff --cached --name-status
```

原稿Markdownもこのリポジトリで公開する場合は、そのファイルを明示的に追加する。非公開の原稿や他の未追跡ファイルをまとめて`git add .`しない。

## 7. コミットしてpushする

新しいページを公開する場合の例：

```bash
git commit -m "feat(pages): <ページ名>を公開"
git push origin main
```

既存ページを更新する場合の例：

```bash
git commit -m "feat(pages): <ページ名>の内容を更新"
git push origin main
```

READMEや手順だけを更新する場合は`docs`を使う。

```bash
git commit -m "docs(readme): 公開コンテンツの案内を更新"
```

## 8. GitHub Pagesの反映を確認する

Pagesは`main`の`/docs`から公開する設定が完了しているため、通常は毎回設定し直す必要はない。push後にGitHubのActionsまたはPages設定画面でビルド完了を確認する。

確認先：

- [GitHub Pages設定](https://github.com/haino357/PublicKnowldgeNote/settings/pages)
- [公開トップ](https://haino357.github.io/PublicKnowldgeNote/)

新しいページは次のURLで確認する。

```text
https://haino357.github.io/PublicKnowldgeNote/<page-slug>/
```

反映まで数分かかる場合がある。公開後はPCとスマートフォンの両方で最終確認する。

## 9. URLを共有する

共有時は、GitHub上のHTMLファイルURLではなく、GitHub PagesのURLを渡す。

```text
共有する
https://haino357.github.io/PublicKnowldgeNote/<page-slug>/

共有しない
https://github.com/haino357/PublicKnowldgeNote/blob/main/docs/<page-slug>/index.html
```

必要に応じて、公開日、対象読者、内容を一文で添える。

## 既存ページを更新するとき

1. 原稿Markdownを修正する
2. 同じslugの`index.html`へ変更を反映する
3. `docs/index.html`の説明も必要なら更新する
4. ローカルで表示とリンクを確認する
5. 対象ファイルだけをステージする
6. コミットして`main`へpushする
7. 公開URLで反映を確認する

slugを変えると公開URLも変わる。すでに共有したURLを維持するため、原則として既存ディレクトリ名は変更しない。

## よくある問題

### 404になる

- `docs/<page-slug>/index.html`が`main`へpushされているか確認する
- URLの大文字・小文字とslugが一致しているか確認する
- GitHub Pagesの公開元が`main`・`/docs`になっているか確認する
- Pagesのビルド完了まで待つ

### CSSや画像が表示されない

- `/assets/...`ではなく`./assets/...`などの相対パスを使う
- ファイル名の大文字・小文字を確認する
- 画像ファイルもコミットされているか確認する

### 変更が公開ページへ反映されない

- `git status`で未コミットの変更が残っていないか確認する
- `git log -1`で目的のコミットがあるか確認する
- `git status --branch --short`で`origin/main`との差を確認する
- GitHubのActionsまたはPages設定画面で失敗していないか確認する

## 公開前チェックリスト

- [ ] Markdown原稿の結論と対象読者が明確
- [ ] 公開してはいけない情報を含んでいない
- [ ] slugが半角英小文字・数字・ハイフンになっている
- [ ] `docs/<page-slug>/index.html`へ配置した
- [ ] `docs/index.html`へリンクを追加した
- [ ] PCとスマートフォンで表示を確認した
- [ ] HTML、JavaScript、リンクを確認した
- [ ] 関係のない変更をステージしていない
- [ ] `main`へpushした
- [ ] GitHub Pagesの公開URLで最終確認した

## 関連リンク

- [PublicKnowldgeNote README](../README.md)
- [GitHub Pagesの公開元を構成する](https://docs.github.com/ja/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
- [GitHub Pagesサイトを作成する](https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site)

