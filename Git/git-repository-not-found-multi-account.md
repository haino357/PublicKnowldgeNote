# git fetch の「Repository not found」と GitHub 複数アカウント

GitHub アカウントを複数使い分けている Mac で、プライベートリポジトリの `git fetch` が `Repository not found` になったときの原因と対処の記録。

## 症状

VS Code の Git 出力に次のエラーが出て、fetch / pull / push ができない。

```text
> git fetch --prune
remote: Repository not found.
fatal: repository 'https://github.com/<owner>/<private-repo>.git/' not found
```

- リモート URL に誤りはなく、リポジトリも GitHub 上にある
- 同じ owner でも **Public リポジトリは成功し、Private リポジトリだけ失敗する**

## 原因

**git がリポジトリにアクセスできない別アカウントの認証情報で接続していた。**

- git の認証情報は `credential.helper = osxkeychain`（macOS キーチェーン）から取得されていた
- キーチェーンに保存されていた `github.com` の認証情報は、**業務用アカウント**のものだった
- リポジトリの owner は**個人アカウント**で、業務用アカウントにはそのプライベートリポジトリへのアクセス権がない
- GitHub は、アクセス権のない相手にはプライベートリポジトリの存在を明かさない。そのため 403 ではなく **404（Repository not found）** が返る
- `gh` CLI では個人アカウントがアクティブだったが、git は `gh` の認証を使っていなかった

> [!note] ポイント
> 「Repository not found」は、URL の誤りだけでなく**認証アカウントの取り違え**でも出る。Public は認証なしで読めるので成功し、Private だけが失敗するのが見分け方。

## 調査手順

```bash
# 1. リモート URL を確認する
git -C <repo> remote -v

# 2. リポジトリが実在するか、gh（個人アカウント）で確認する
gh repo view <owner>/<private-repo> --json name,visibility

# 3. git が使う認証ヘルパーを確認する
git config --get credential.helper

# 4. git が実際にどのアカウントで認証しているかを確認する（パスワード行は表示しない）
printf "protocol=https\nhost=github.com\n\n" | git credential fill | grep username

# 5. gh にログイン済みのアカウントとアクティブなアカウントを確認する
gh auth status

# 6. プロンプトを出さずに各リポジトリへの接続を試す
GIT_TERMINAL_PROMPT=0 git -C <repo> ls-remote --heads origin >/dev/null && echo OK || echo NG
```

手順 2 でリポジトリが実在し、手順 4 のユーザー名が owner 側のアカウントと違えば、この原因と判断できる。

## 対処法

### 1. リポジトリごとにアカウントを指定する（推奨）

業務用リポジトリはこれまでどおりキーチェーンの認証情報を使い、個人リポジトリだけを個人アカウントに切り替える方法。

```bash
cd <repo>

# URL にユーザー名を入れ、どのアカウントで接続するかを指定する
git remote set-url origin https://<個人アカウント>@github.com/<owner>/<private-repo>.git

# このリポジトリだけ、gh に保存済みのトークンを使う
git config credential.helper ""
git config --add credential.helper "!gh auth git-credential"
```

- `credential.helper ""` で、グローバル設定の `osxkeychain` をこのリポジトリでは無効にする
- 個人アカウントの Private リポジトリ全部に、同じ設定をする

> [!warning] 注意
> `gh auth git-credential` は、**`gh` でアクティブなアカウント**のトークンしか返さない。URL のユーザー名がアクティブでないアカウントだと何も返らず、認証に失敗する。`gh auth switch` で業務用アカウントに切り替えたままだと、個人リポジトリの fetch は失敗する。
>
> アカウントを頻繁に切り替える場合は、`gh` を使わずに対処する。URL にユーザー名を入れたうえで `osxkeychain` のままにし、個人アカウントの Personal Access Token をそのユーザー名でキーチェーンに保存する。osxkeychain はユーザー名ごとに認証情報を分けて保存するので、アカウントが混ざらない。

### 2. git 全体で gh の認証を使う

```bash
gh auth setup-git
```

手軽だが、すべてのリポジトリで `gh` のアクティブアカウントが使われる。業務用リポジトリを扱うたびに `gh auth switch` が必要になる。

### 3. キーチェーンの登録を差し替える

「キーチェーンアクセス」で `github.com` の項目を個人アカウントのものに替える。今度は業務用リポジトリ側が失敗するので、複数アカウント運用には向かない。

## 確認

```bash
git -C <repo> fetch --prune
```

エラーが出なければ解決。手順 6 の `ls-remote` で、全リポジトリが OK になることも確認する。

## 関連

- [[git-worktree-guide]]
