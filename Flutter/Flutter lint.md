---
created: 2025-12-09T09:56:00
update: 2025-12-09
tags:
  - Flutter
  - Flutter_Lint
投稿先: Qiita
投稿先URL: https://qiita.com/hai_haino/items/cda48a0e27e7054b689b
投稿タイトル: FlutterのLintに関してまだまだ何も知らなかった件
---
# Flutter Lint概要
公式推奨の `flutter_lints` を `pubspec.yaml` の `dev_dependencies` に入れ、`analysis_options.yaml` で取り込むところから始まる。

# Lintの重要性
チーム開発において二人以上で開発を実施する場合に効果を発揮する。その効果は下記。
- コードの一貫性
- バグの早期発見
- コードレビューの効率化

## コードの一貫性
コードスタイルや規約を統一することができる。それにより個人による差をなくし、チーム全員が同じコーディングスタイルにすることができる。

## バグの早期発見
未使用の変数やメソッドなどコンパイル時には見逃しがちな問題を警告することで潜在的なバグを予防する。

## コードレビューの効率化
コードスタイルの違いによる指摘を低減させることで本質的なロジックの設計にリソースを集中することができる。これにより品質の向上やリリーススピードを上げる。

# 導入手順
1. `pubspec.yaml` に `flutter_lints` を追加し `flutter pub get`。
2. プロジェクト直下に `analysis_options.yaml` を置く。
   ```yaml
   include: package:flutter_lints/flutter.yaml

   analyzer:
     language:
       strict-inference: true
       strict-raw-types: true

   linter:
     rules:
       # 追加で強めたいルールだけ書く
       always_use_package_imports: true
       directives_ordering: true
       prefer_const_constructors: true
       prefer_const_declarations: true
       prefer_const_literals_to_create_immutables: true
       use_build_context_synchronously: true
       avoid_print: true
   ```

# 初期導入で最低限入れるルール
- `include: package:flutter_lints/flutter.yaml` をベースに使う（公式推奨）。
- 型の厳格化: `strict-inference: true`, `strict-raw-types: true`。
- import統一: `always_use_package_imports`, `directives_ordering`。
- const活用: `prefer_const_constructors`, `prefer_const_declarations`, `prefer_const_literals_to_create_immutables`。
- スタイル統一: `require_trailing_commas`, `prefer_single_quotes`。
- 非同期安全: `use_build_context_synchronously`。
- ログ抑制: `avoid_print`（本番ではlogger等に寄せる）。
- 無駄削減: `avoid_unnecessary_containers`, `avoid_annotating_with_dynamic`。

最小セット例（上記をまとめた形）:
```yaml
include: package:flutter_lints/flutter.yaml

analyzer:
  language:
    strict-inference: true
    strict-raw-types: true

linter:
  rules:
    # スタイル
    require_trailing_commas: true
    prefer_single_quotes: true
    always_use_package_imports: true
    directives_ordering: true

    # 安全性
    use_build_context_synchronously: true
    avoid_annotating_with_dynamic: true
    avoid_print: true

    # パフォーマンス
    prefer_const_constructors: true
    prefer_const_declarations: true
    prefer_const_literals_to_create_immutables: true
    avoid_unnecessary_containers: true
```

# 初期設定から追加するべきルール（例）
```
# Lintルールの設定
linter:
  rules:
    # スタイル関連
    prefer_single_quotes: true # シングルクォートでないと警告が出る
    require_trailing_commas: true # 末尾カンマを必須に
    always_use_package_imports: true # インポートは絶対パスにする（相対パスでインポートすると警告が出る）
    directives_ordering: true # インポート順序を強制
    sort_child_properties_last: true # Widgetのchildを最後に配置

    # エラー防止
    avoid_annotating_with_dynamic: true # dynamicで型をつけると警告が出る（dynamicアノテーションを禁止）
    use_build_context_synchronously: true # 非同期後のBuildContext使用をチェック

    # パフォーマンス
    avoid_unnecessary_containers: true # 不要なContainerを禁止
    prefer_const_constructors: true # 可能な場合はconstを使用
    prefer_const_declarations: true # 定数宣言にconstを使用

analyzer:
  language:
    strict-inference: true # 厳密な型推論を有効化
    strict-raw-types: true # ジェネリクスの型指定を強制
```

# テスト向けの設定
- テストだけ緩め/強めたい場合は `test/analysis_options.yaml` を置き、ルート設定を継承して上書きする。
- 例（テストを強める場合）:
  ```yaml
  # test/analysis_options.yaml
  include: ../analysis_options.yaml

  linter:
    rules:
      avoid_print: true           # テストでもprintを避ける
      unawaited_futures: true     # await漏れ検知
      prefer_final_locals: true   # ローカルを極力finalに
      prefer_const_constructors: true
  ```
- 例（テストで緩める場合はseverity調整）:
  ```yaml
  analyzer:
    errors:
      avoid_print: info  # テスト内ではprint許容にする
  ```

# lint修正の自動適用
`analysis_options.yaml` に設定したルールに沿って、自動で修正できるものを反映する。
- コマンド: `dart fix --apply ./lib`
- 対象: `./lib` 以下のDart/Flutterコード
- 内容: 未使用import削除、相対importをpackage:に置換、const付与、末尾カンマ付与など、lintで機械的に直せる部分をまとめて修正
- 運用: ルールを変えたあとや大きなリファクタ後に一度流すと、手動修正の手間が減る
- テストにも適用する場合: `dart fix --apply ./test` を追加実行する（`test/analysis_options.yaml` を作っている場合は、必ず `include: ../analysis_options.yaml` でルールを継承しておく）

また、上記のようにルールを追加した場合は、上記コマンドで一気に修正することができる。
