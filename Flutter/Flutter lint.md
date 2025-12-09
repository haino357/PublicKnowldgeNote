---
tags:
  - Flutter
  - Flutter_Lint
---
# 概要
プロジェクトを初期設定から `pibspec.yaml` に追加されている。

# Lintの重要性
チーム開発で二人以上で開発を実施する場合に効果を発揮する。その効果は下記。
- コードの一貫性
- バグの早期発見
- コードレビューの効率化

## コードの一貫性
コードスタイルや規約を統一することができる。それにより個人による差をなくし、チーム全員が同じコーディングスタイルにすることができる。

## バグの早期発見
未使用の変数やメソッドなどコンパイル時には見逃しがちな問題を警告することで潜在的なバグを予防する。

## コードレビューの効率化
コードスタイルの違いによる指摘を低減させることで本質的なロジックの設計にリソースを集中することができる。これにより品質の向上やリリーススピードを上げる。

# 初期設定から追加するべきルール
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

    # パフォーマンス
    avoid_unnecessary_containers: true # 不要なContainerを禁止
    prefer_const_constructors: true # 可能な場合はconstを使用
    prefer_const_declarations: true # 定数宣言にconstを使用

  analyzer:
    language:
      strict-inference: true # 厳密な型推論を有効化
      strict-raw-types: true # ジェネリクスの型指定を強制
```

下記にそれぞれを説明する。
## 相対パスを禁止するルール
`analysis_options.yaml` に下記を追加する。
```
# 相対パスのimportを禁止し、pavkage:形式を強制
always_use_package_imports: true
```
また、上記を追加すると下記コマンドで一気に修正することができる。
`dart fix --apply ./lib`
