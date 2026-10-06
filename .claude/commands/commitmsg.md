---
description: 現在のステージングの内容を確認し、コミットメッセージ案を提案する（ステージもコミットも実行しない）
---

# /commitmsg — コミットメッセージ提案

すでに `git add` されているステージ済み変更を確認し、コミットメッセージ案を **1〜3 候補** 提示する。**`git add` と `git commit` は実行しない**（責務は提案のみ）。

WorkTaskBoard の `/commitmsg` を、PublicKnowldgeNote 向けに調整したもの。`/stage` がステージ + 提案をセットで行うのに対し、本コマンドは **既にステージ済みの内容に対する純粋なメッセージ提案** を担当する（部分ステージや `git add -p` の後など）。

## 動作仕様

### 1. 引数

引数なし。常に現在のステージ内容を対象とする。

### 2. 処理フロー

1. 並列実行で現状確認：
   - `git diff --staged --stat`（ステージ済みファイル一覧と変更行数）
   - `git diff --staged`（ステージ済み差分の中身）
   - `git status --short`（未ステージ・未追跡ファイルの把握）
   - `git log -5 --oneline`（過去のコミットスタイル参照）

2. ステージが空の場合は警告を出して終了：
   ```
   ⚠️ ステージ済み変更がありません。/stage または git add 後に再実行してください。
   ```

3. ステージ内容を要約表示：
   - 変更ファイル一覧（追加 / 修正 / 削除 / リネーム）
   - 主要な変更点（数行〜十数行レベル）
   - 関連性の薄い変更が混在している場合は分割の助言
   - 未ステージ・未追跡の変更があれば、今回のコミットに含まれないことを明示

4. **コミットメッセージ案を 1〜3 候補提示**：
   - **Conventional Commits 接頭辞を必ず付与する**（下記「3. 接頭辞の判定」参照）
   - 接頭辞の後は **日本語の descriptive な件名**、短く（70 文字以内目安）、語尾は「追加 / 修正 / 更新 / 整理」など
   - 件名のフォーマット：`<type>: <subject>` または `<type>(<scope>): <subject>`
   - 末尾に `Co-Authored-By: <実行中のClaudeのモデル名> <noreply@anthropic.com>` を入れる（モデル名は固定せず、実行時のセッションのものを使う）
   - 候補は「簡潔版 / scope 付き版 / 情報量増し版（本文あり）」のような切り口で 1〜3 案

5. 採用候補の指示があれば、その後の `git commit` 実行手順を案内（HEREDOC 形式の例文を提示）。実行はユーザーの指示を待つ

### 3. 接頭辞の判定

ステージ内容から決める。**複数カテゴリにまたがる場合は dominant change（主たる変更）で選ぶ**。`/stage` と同じ判定表を使う。

| 接頭辞 | 適用条件 | このリポジトリでの例 |
|--------|---------|------------|
| `feat:` | 公開ページ・記事機能・新しい仕組みの追加 | `docs/<page-slug>/index.html` の新規公開、記事の公開状態への更新 |
| `fix:` | 誤り・リンク切れ・表示崩れの修正 | Basesの抽出条件の修正、記事内リンクの修正 |
| `docs:` | 記事・技術ノートの追加・更新 | `Articel/`、`Flutter/`、`VSCode/`、`Git/`、`mobile/`、`README.md`、`AGENTS.md` |
| `refactor:` | 内容は同等のままの構造変更 | カテゴリの再編、記事管理方法の変更 |
| `chore:` | 設定系・メタファイル・ハウスキーピング | `.claude/`、`.gitignore` |
| `style:` | フロントマター整形・空行調整など意味を変えない整形 | YAMLプロパティ順の統一、表の整形 |

**scope（任意）**：`<type>(<scope>): <subject>` 形式で付けられる場合は付ける。このリポジトリでよく使う scope：

- `docs(article)`, `feat(article)` — `Articel/` の記事
- `feat(pages)`, `feat(docs)` — `docs/` 配下の公開Webページ
- `docs(vscode)`, `docs(obsidian)`, `docs(readme)` — 分野別の技術ノート・README
- `fix(bases)` — `.base` ファイル
- `chore(claude)` — `.claude/` 配下

**判定の例**：

| ステージ内容 | 接頭辞 |
|------------|-------|
| `Articel/` に下書き記事を追加 | `docs(article):` |
| 記事を公開状態に更新しサムネイルを追加 | `feat(article):` |
| `docs/<page-slug>/index.html` を新規追加 | `feat(pages):` |
| `.base` の抽出条件の誤りを修正 | `fix(bases):` |
| `VSCode/` の設定メモを更新 | `docs(vscode):` |
| `.claude/commands/stage.md` を追加 | `chore(claude):` |

### 4. 制約

- **`git add` を絶対に実行しない**（このコマンドは閲覧のみ）
- **`git commit` と `git push` を絶対に実行しない**
- **ステージ状態を変更しない**（`git restore --staged` も提案だけにとどめる）
- **`--no-verify` などフックスキップは絶対に提案しない**
- 機密ファイル（`.env`, `*.key`, `*.pem`, `*.p12`, `credentials*`, `secret*`, `token*` 等）がステージ済みの場合は強く警告し、`git restore --staged <path>` を提案する
- 関連性の薄い変更が混在している場合は分割コミットを提案する（強制はしない）

## 使用例

```
/commitmsg
→ ステージ内容を確認 → 接頭辞付きメッセージ案を 1〜3 候補提示
   例: `docs(article): Dashboard運用の記事を追加`
       `fix(bases): Article一覧の抽出条件を修正`

（ステージ無し）
/commitmsg
→ ⚠️ ステージ済み変更がありません警告 → /stage を案内

（機密ファイル混入）
/commitmsg
→ ⚠️ .env が含まれている警告 → git restore --staged 案内
```

## 関連

- **`/stage`** — ステージング + メッセージ提案をセットで行う上位コマンド。接頭辞の判定表は共通
- `AGENTS.md` — 作業前の変更・ステージ状態の維持、事前承認が必要な操作
- このコマンドは「ステージ済みかつ部分コミット直前」のワークフローを担当
