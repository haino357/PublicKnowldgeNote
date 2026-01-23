# CI/CDとは
Continuous Integration／Continuous Delivery & Deployment
継続的インティグレーション／継続的デリバリー

CI/CDは1つの技術を指すものでなく、ソフトウェアの変更を常にテストして自動で本番環境にリリース可能な状態にしておく、ソフトウェア開発の手法を意味します。CI/CDを取り入れると、バグを素早く発見したり、変更を自動でリリースしたりできるようになります。

CI/CDには大きく分けてオンプレミス型とクラウド型があり、オンプレミス型としてはJenkins、クラウド型としてはTravis CIやCircleCIなどが有名です。オンプレミス型は一般的に拡張性が高い一方、自分たちで構築・運用する管理コストが発生します。クラウド型は拡張性が低いですが、サーバーなどを自前で用意する必要がなく、すぐに使い始めることができるのが大きな魅力です。

オンプレミス型
- [Jenkins](https://jenkins.io/)
- [Concourse CI](https://concourse-ci.org/)
- [Drone(クラウド版もあり）](http://try.drone.io/)

クラウド型
- [Travis CI](https://travis-ci.org/)
- [CircleCI](https://circleci.jp/)
- [Wercker](http://www.wercker.com/platform)
- [Codeship](https://codeship.com/)
- [AWS CodeBuild](https://aws.amazon.com/jp/codebuild/)
- [GCP Cloud Build](https://cloud.google.com/cloud-build/?hl=ja)

## 目的・適用範囲
- 対象リポジトリ／サービス
- どの環境まで自動化するか（build/test/deploy）

## CIとCDの定義
- CI（継続的インテグレーション）：ビルド・テスト・静的解析の自動化
- CD（継続的デリバリー/デプロイ）：成果物の自動配布や本番反映の自動化

## 代表的なパイプライン構成
1. Checkout
2. Install
3. Lint / Format
4. Test（ユニット／統合）
5. Build
6. Security Scan
7. Deploy

## トリガー
- push
- pull request
- タグ（release）
- 手動実行
- スケジュール実行（夜間バッチなど）

## 環境分離と承認
- dev / stg / prod の分離
- 本番は手動承認を入れる
- デプロイ権限は最小限にする

## Secrets / 環境変数
- Secretsで管理し、リポジトリに直書きしない
- 権限は用途ごとに分離する

## キャッシュ
- 依存関係のキャッシュでビルド高速化
- キャッシュ破棄条件を決めておく

## 失敗時の対応
- リトライ回数を決める
- 通知先を明確にする（Slack/メール）
- 失敗ログの見方をメモしておく

## ロールバック
- 直前の成果物を即時戻せるようにする
- DB変更はロールバック手順を別途記載

## GitHub Actionsの最小例
```
name: CI

on:
  push:
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
      - name: Install
        run: npm ci
      - name: Test
        run: npm test
```

## ベストプラクティス
- テストは並列化する
- 失敗時の再現ができるようログを残す
- 依存関係の更新は自動化する
