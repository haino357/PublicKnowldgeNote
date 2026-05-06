---
tags:
  - Flutter
  - 開発環境
  - macOS
  - iOS
  - Android
  - CI/CD
created: 2026-03-03
---

# 📱 Flutter 初期開発環境構築ガイド（macOS）

Flutter 開発環境をゼロからセットアップし、iOS / Android 両対応のアプリ開発を始めるための包括ガイド。

---

## 🔧 1. 前提条件と事前準備

### macOS バージョン要件

| 項目 | 要件 |
|---|---|
| macOS | **Sequoia 15.2** 以上（Xcode 26.x 使用時） |
| ディスク空き容量 | 最低 **20 GB**（Xcode + Android Studio + SDK 含む） |
| CPU | Apple Silicon（M1 以上）推奨 |

### Homebrew のインストール

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

インストール後、PATH を通す（Apple Silicon の場合）:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Git のインストール

```bash
brew install git
git --version
```

### Rosetta 2（Apple Silicon Mac のみ）

一部のツールが Intel バイナリを使用するため、Rosetta 2 をインストールしておく:

```bash
sudo softwareupdate --install-rosetta --agree-to-license
```

---

## 📦 2. Flutter SDK のインストール

### 方法 A: FVM によるバージョン管理（推奨）

FVM（Flutter Version Management）を使うと、プロジェクトごとに Flutter バージョンを切り替えられる。チーム開発では必須。

```bash
# Homebrew でインストール
brew tap leoafarias/fvm
brew install fvm
```

> [!note] FVM 4.0 の新機能
> FVM 4.0 では Fork リポジトリ対応、Monorepo（melos.yaml）サポート、モジュラーアーキテクチャなどが追加された。

```bash
# stable チャネルの最新版をインストール
fvm install stable

# プロジェクトで使用するバージョンを指定
fvm use stable

# バージョン確認
fvm flutter --version
```

FVM 使用時は `flutter` コマンドの代わりに `fvm flutter` を使う:

```bash
fvm flutter run
fvm flutter pub get
fvm flutter doctor
```

> [!tip] エイリアスの設定
> 毎回 `fvm flutter` と打つのが面倒な場合、シェルにエイリアスを設定する:
> ```bash
> echo 'alias f="fvm flutter"' >> ~/.zshrc
> echo 'alias d="fvm dart"' >> ~/.zshrc
> source ~/.zshrc
> ```

### 方法 B: 直接インストール

```bash
# Homebrew 経由
brew install --cask flutter

# または公式サイトから手動ダウンロード
# https://docs.flutter.dev/get-started/install/macos
```

### PATH の設定と確認

直接インストールした場合、`~/.zshrc` に以下を追加:

```bash
export PATH="$HOME/development/flutter/bin:$PATH"
```

確認:

```bash
flutter --version
# Flutter 3.41.x • channel stable • Dart 3.11.x
```

---

## 🍎 3. iOS 開発環境の構築

### Xcode のインストール

```bash
# App Store から Xcode をインストール（推奨: Xcode 26.3）
# または xcode-select でコマンドラインツールを先にインストール
xcode-select --install
```

> [!warning] App Store 要件
> 2025 年 4 月以降、App Store への提出には **iOS 18 SDK 以上**（Xcode 16.1+）が必須。最新の Xcode 26.x を推奨。

Xcode 初回起動後、ライセンスに同意:

```bash
sudo xcodebuild -runFirstLaunch
sudo xcodebuild -license accept
```

### CocoaPods と Swift Package Manager

Flutter は現在 **CocoaPods** から **Swift Package Manager（SPM）** への移行期にある。

| 時期 | 状況 |
|---|---|
| 現在（2026 年 3 月） | SPM + CocoaPods 両方サポート |
| 2026 年 Q2 | 一部 iOS SDK が CocoaPods での新バージョン配布を停止 |
| 2026 年 12 月 | CocoaPods trunk が **読み取り専用**に |

**SPM を有効化**（推奨）:

```bash
flutter config --enable-swift-package-manager
```

**CocoaPods も念のためインストール**（SPM 未対応プラグインのフォールバック用）:

```bash
brew install cocoapods
# または
sudo gem install cocoapods
```

> [!note] SPM のメリット
> SPM は Xcode にバンドルされているため、Ruby や CocoaPods を別途インストールする必要がない。全プラグインが SPM 対応すれば CocoaPods は不要になる。

### iOS シミュレータ

```bash
# シミュレータを開く
open -a Simulator

# 利用可能なシミュレータ一覧
xcrun simctl list devices
```

### 実機デバッグ

1. iPhone を USB で Mac に接続
2. Xcode → Window → Devices and Simulators で信頼を確認
3. Apple Developer アカウントを Xcode に登録（Xcode → Settings → Accounts）
4. プロジェクトの Signing & Capabilities で Team を選択

```bash
flutter run -d <device-id>

# 接続されたデバイス一覧
flutter devices
```

---

## 🤖 4. Android 開発環境の構築

### Android Studio のインストール

```bash
brew install --cask android-studio
```

> [!note] 推奨バージョン
> **Android Studio Narwhal 2025.1.1 Patch 1** 以降を使用する。初期リリースには Flutter プロジェクト作成ウィザードのバグがあった。

### SDK と必要なコンポーネントの設定

Android Studio を起動し、SDK Manager で以下をインストール:

- **SDK Platforms**: Android 15（API 35）
- **SDK Tools**:
  - Android SDK Build-Tools
  - Android SDK Command-line Tools
  - Android Emulator
  - Android SDK Platform-Tools

### Java の設定

Flutter は **Java 17（Temurin）** を推奨:

```bash
# Homebrew でインストール
brew install --cask temurin@17

# Flutter に Java パスを設定
flutter config --jdk-dir /Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home
```

> [!warning] Java バージョンの注意
> Android Studio Ladybug（2024.2.1）以降は JDK 21 がバンドルされているが、Flutter のビルドには JDK 17 が必要。`flutter config --jdk-dir` で明示的に指定すること。

### 推奨ビルドツールバージョン

| ツール | バージョン |
|---|---|
| Android Gradle Plugin（AGP） | 8.9.1 |
| Gradle | 8.11.1 |
| Java | 17（Temurin） |
| Target SDK | 35（Android 15） |

### エミュレータの作成（Apple Silicon 対応）

1. Android Studio → Virtual Device Manager → Create Virtual Device
2. デバイスプロファイル選択（例: Pixel 8）
3. システムイメージ: **arm64-v8a** を選択（Apple Silicon 必須）
4. Finish で作成

```bash
# コマンドラインからエミュレータ起動
flutter emulators --launch <emulator-id>
```

### ライセンス承諾

```bash
flutter doctor --android-licenses
# すべて "y" で承諾
```

---

## 🛠️ 5. IDE のセットアップ

### VS Code（推奨）

```bash
brew install --cask visual-studio-code
```

#### 必須拡張機能

| 拡張機能 | ID | 説明 |
|---|---|---|
| **Flutter** | `Dart-Code.flutter` | Flutter コア機能（Hot Reload, デバッガ, Widget Inspector） |
| **Dart** | `Dart-Code.dart-code` | Dart 言語サポート（Flutter 拡張と同時にインストールされる） |

#### 推奨拡張機能

| 拡張機能 | 説明 |
|---|---|
| **Error Lens** | エラー・警告をコード行末にインライン表示 |
| **Riverpod Snippets** | Riverpod のボイラープレート生成 |
| **Bloc** (`felangel.bloc`) | BLoC / Cubit のコード生成スニペット |
| **Pubspec Assist** | `Cmd+Shift+P` から依存関係を追加 |
| **Flutter Stylizer** | Widget コンストラクタのパラメータ順を統一 |
| **Awesome Flutter Snippets** | よく使うパターンのスニペット集 |
| **Todo Tree** | `// TODO` コメントをサイドバーに集約 |
| **Mason** (`BrickhubOrg.mason`) | テンプレートベースのコード生成 |
| **GitLens** | Git 統合の強化 |

#### VS Code の推奨設定（`settings.json`）

```json
{
  "dart.flutterSdkPath": ".fvm/versions/stable",
  "dart.lineLength": 80,
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": "explicit",
    "source.organizeImports": "explicit"
  },
  "[dart]": {
    "editor.defaultFormatter": "Dart-Code.dart-code",
    "editor.rulers": [80],
    "editor.selectionHighlight": false,
    "editor.suggestSelection": "first",
    "editor.tabCompletion": "onlySnippets",
    "editor.wordBasedSuggestions": "off"
  }
}
```

> [!tip] FVM 使用時の設定
> `dart.flutterSdkPath` に `.fvm/versions/stable` を指定すると、VS Code が FVM で管理された Flutter SDK を自動検出する。

### Android Studio のプラグイン

Android Studio をメインエディタとして使う場合:

1. Plugins → Marketplace で **Flutter** を検索しインストール（Dart も同時にインストールされる）
2. File → Settings → Languages & Frameworks → Flutter で SDK パスを設定

### analysis_options.yaml

プロジェクトルートに配置する静的解析設定:

```yaml
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    # エラー防止
    avoid_print: true
    avoid_relative_lib_imports: true
    prefer_const_constructors: true
    prefer_const_declarations: true
    prefer_final_fields: true
    prefer_final_locals: true

    # コードスタイル
    always_declare_return_types: true
    annotate_overrides: true
    prefer_single_quotes: true
    sort_constructors_first: true
    unawaited_futures: true
    unnecessary_lambdas: true

analyzer:
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"
  errors:
    invalid_annotation_target: ignore
```

---

## ✅ 6. flutter doctor による環境検証

```bash
flutter doctor -v
```

理想的な出力:

```
[✓] Flutter (Channel stable, 3.41.x)
[✓] Android toolchain - develop for Android devices (Android SDK version 35.0.x)
[✓] Xcode - develop for iOS and macOS (Xcode 26.3)
[✓] Chrome - develop for the web
[✓] Android Studio (2025.1)
[✓] VS Code (version 1.9x.x)
[✓] Connected device (x available)
[✓] Network resources

• No issues found!
```

### よくあるエラーと対処法

#### `✗ Android license status unknown`

```bash
flutter doctor --android-licenses
```

#### `✗ CocoaPods not installed`

```bash
brew install cocoapods
# または SPM を有効化して CocoaPods を不要にする
flutter config --enable-swift-package-manager
```

#### `✗ cmdline-tools component is missing`

Android Studio → SDK Manager → SDK Tools → **Android SDK Command-line Tools** にチェックを入れて Apply

#### `! Java version issue`

```bash
flutter config --jdk-dir /Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home
```

#### `✗ Xcode not found`

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

---

## 🚀 7. プロジェクト作成と初期設定

### flutter create

```bash
# 基本的なプロジェクト作成
flutter create my_app

# 組織名とプラットフォーム指定
flutter create --org com.example --platforms ios,android my_app

# FVM 使用時
fvm flutter create --org com.example --platforms ios,android my_app
```

### プロジェクトに FVM を設定

```bash
cd my_app
fvm use stable
```

### pubspec.yaml の初期設定例

```yaml
name: my_app
description: "A new Flutter project."
publish_to: "none"
version: 1.0.0+1

environment:
  sdk: ^3.11.0
  flutter: ">=3.41.0"

dependencies:
  flutter:
    sdk: flutter
  # 状態管理
  flutter_riverpod: ^3.0.0
  riverpod_annotation: ^3.0.0
  # ルーティング
  go_router: ^14.0.0
  # HTTP
  dio: ^5.0.0
  # ローカルストレージ
  shared_preferences: ^2.0.0
  # DI
  get_it: ^8.0.0
  injectable: ^2.0.0
  # ユーティリティ
  freezed_annotation: ^2.0.0
  json_annotation: ^4.0.0
  intl: ^0.19.0

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^5.0.0
  # コード生成
  build_runner: ^2.0.0
  riverpod_generator: ^3.0.0
  freezed: ^2.0.0
  json_serializable: ^6.0.0
  injectable_generator: ^2.0.0
  # テスト
  mocktail: ^1.0.0
```

### .gitignore の追加項目

```gitignore
# Flutter / Dart
*.dart_tool/
.packages
build/
.flutter-plugins
.flutter-plugins-dependencies

# FVM
.fvm/versions

# コード生成
*.g.dart
*.freezed.dart

# IDE
.idea/
*.iml
.vscode/

# macOS
.DS_Store

# 秘密情報
*.env
*.jks
*.keystore
google-services.json
GoogleService-Info.plist
```

### 初回ビルドと Hot Reload 確認

```bash
cd my_app

# 依存関係の取得
flutter pub get

# iOS シミュレータで起動
flutter run

# Android エミュレータで起動
flutter run -d emulator-5554
```

> [!tip] Hot Reload / Hot Restart
> - **Hot Reload**（`r`）: ステートを保持したまま UI を更新
> - **Hot Restart**（`R`）: ステートをリセットして再起動
> - **Full Restart**（`Shift+R` または再ビルド）: アプリを完全に再起動

---

## 📂 8. ディレクトリ構成とベストプラクティス

### Clean Architecture ベースの推奨構成

**Feature-first + レイヤー分離**が現在のベストプラクティス:

```
lib/
├── core/                          # アプリ全体で使う共通コード
│   ├── error/
│   │   ├── exceptions.dart        # 例外クラス
│   │   └── failures.dart          # Failure クラス
│   ├── network/
│   │   └── network_info.dart      # ネットワーク接続チェック
│   ├── usecase/
│   │   └── usecase.dart           # UseCase の抽象基底クラス
│   └── utils/
│       └── constants.dart         # 定数
│
├── features/                      # 機能ごとのディレクトリ
│   ├── auth/
│   │   ├── data/
│   │   │   ├── datasources/
│   │   │   │   ├── auth_local_datasource.dart
│   │   │   │   └── auth_remote_datasource.dart
│   │   │   ├── models/
│   │   │   │   └── user_model.dart
│   │   │   └── repositories/
│   │   │       └── auth_repository_impl.dart
│   │   ├── domain/
│   │   │   ├── entities/
│   │   │   │   └── user.dart       # 純粋な Dart クラス
│   │   │   ├── repositories/
│   │   │   │   └── auth_repository.dart  # 抽象インターフェース
│   │   │   └── usecases/
│   │   │       ├── login_usecase.dart
│   │   │       └── logout_usecase.dart
│   │   └── presentation/
│   │       ├── notifiers/          # Riverpod の場合
│   │       │   └── auth_notifier.dart
│   │       ├── pages/
│   │       │   └── login_page.dart
│   │       └── widgets/
│   │           └── login_form.dart
│   │
│   └── home/
│       └── ...                    # 同じ構成を各 feature で繰り返す
│
├── shared/                        # Feature 間で共有するリソース
│   ├── domain/
│   │   └── entities/
│   └── data/
│       └── datasources/
│           └── local_database.dart
│
├── injection_container.dart       # DI 設定（get_it / Riverpod）
└── main.dart
```

### レイヤーの責務

| レイヤー | 責務 | 依存方向 |
|---|---|---|
| **Domain** | Entity, Repository インターフェース, UseCase | 何にも依存しない（純粋 Dart） |
| **Data** | Repository 実装, DataSource, Model | Domain に依存 |
| **Presentation** | Widget, State Management, Page | Domain に依存 |

> [!warning] 依存方向のルール
> Domain レイヤーは Flutter をインポートしてはいけない。これにより、ビジネスロジックが完全にテスト可能になる。

### 命名規則

| 種類 | 規則 | 例 |
|---|---|---|
| ファイル名 | `snake_case` | `auth_repository.dart` |
| クラス名 | `PascalCase` | `AuthRepository` |
| 変数・関数 | `camelCase` | `getUserById` |
| 定数 | `camelCase` | `defaultTimeout` |
| プライベート | `_` プレフィックス | `_internalState` |

### アセット管理

```yaml
# pubspec.yaml
flutter:
  assets:
    - assets/images/
    - assets/icons/
    - assets/fonts/

  fonts:
    - family: NotoSansJP
      fonts:
        - asset: assets/fonts/NotoSansJP-Regular.ttf
        - asset: assets/fonts/NotoSansJP-Bold.ttf
          weight: 700
```

ディレクトリ構成:

```
assets/
├── images/
│   ├── logo.png
│   ├── 2.0x/
│   │   └── logo.png
│   └── 3.0x/
│       └── logo.png
├── icons/
└── fonts/
```

---

## 🧠 9. 状態管理の選定ガイド

### 比較表

| 項目 | Riverpod 3.0 | BLoC 9.x | Signals 6.0 | Provider |
|---|---|---|---|---|
| ボイラープレート | 少 | 多 | 少 | 中 |
| 学習コスト | 中 | 高 | 低〜中 | 低 |
| テスタビリティ | ◎ | ◎ | ○ | ○ |
| DevTools 対応 | ○ | ◎ | △ | ○ |
| コード生成 | `@riverpod` 推奨 | `freezed` 併用 | 不要 | 不要 |
| 適用規模 | 小〜大 | 中〜大 | 小〜中 | 小 |
| 状態 | 活発に開発中 | 活発に開発中 | 活発に開発中 | メンテナンスモード |

### 規模別の推奨

| プロジェクト規模 | 推奨 | 理由 |
|---|---|---|
| 個人開発・MVP | **Riverpod 3.0** | バランスが良い。コード生成で型安全 |
| チーム開発・中規模 | **Riverpod 3.0** | コンパイル時安全性、テスト容易性 |
| エンタープライズ・大規模 | **BLoC 9.x** | イベント駆動、監査証跡、厳密な関心の分離 |
| パフォーマンス重視 | **Signals 6.0** | きめ細かい再描画制御、Widget ツリー全体の再構築を回避 |
| 既存アプリ（Provider 使用中） | **Provider 維持** | 安定しており急いで移行する必要はない |

### Riverpod 3.0 の基本セットアップ

```bash
flutter pub add flutter_riverpod riverpod_annotation
flutter pub add --dev riverpod_generator build_runner
```

```dart
// main.dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

void main() {
  runApp(
    const ProviderScope(
      child: MyApp(),
    ),
  );
}
```

```dart
// コード生成を使った Provider 定義
import 'package:riverpod_annotation/riverpod_annotation.dart';

part 'user_provider.g.dart';

@riverpod
Future<List<User>> users(Ref ref) async {
  final repository = ref.watch(userRepositoryProvider);
  return repository.getUsers();
}

// パラメータ付き（.family の代替）
@riverpod
Future<User> user(Ref ref, {required String id}) async {
  final repository = ref.watch(userRepositoryProvider);
  return repository.getUser(id);
}
```

```dart
// Widget での使用
class UserListPage extends ConsumerWidget {
  const UserListPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final usersAsync = ref.watch(usersProvider);

    return usersAsync.when(
      data: (users) => ListView.builder(
        itemCount: users.length,
        itemBuilder: (context, index) => ListTile(
          title: Text(users[index].name),
        ),
      ),
      loading: () => const Center(child: CircularProgressIndicator()),
      error: (error, stack) => Center(child: Text('Error: $error')),
    );
  }
}
```

> [!note] Riverpod 3.0 の新機能
> - **自動リトライ**: ネットワークエラー時に自動でリトライ
> - **Pause/Resume**: 画面外の Provider リスナーを自動停止
> - **オフラインキャッシュ + Mutations**（実験的）: フォーム送信の簡素化
> - **`ProviderContainer.test()`**: テストの書きやすさが向上

### コード生成の実行

```bash
dart run build_runner build --delete-conflicting-outputs

# ファイル監視モード（開発中に便利）
dart run build_runner watch --delete-conflicting-outputs
```

---

## ⚡ 10. CI/CD 基本セットアップ

### GitHub Actions での Flutter CI

```yaml
# .github/workflows/ci.yml
name: Flutter CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"

      - uses: subosito/flutter-action@v2
        with:
          flutter-version-file: pubspec.yaml
          channel: stable
          cache: true

      - name: Install dependencies
        run: flutter pub get

      - name: Analyze
        run: flutter analyze

      - name: Format check
        run: dart format --set-exit-if-changed .

      - name: Run tests
        run: flutter test --coverage

  build-android:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"

      - uses: subosito/flutter-action@v2
        with:
          flutter-version-file: pubspec.yaml
          channel: stable
          cache: true

      - run: flutter pub get

      - name: Build APK
        run: flutter build apk --release

      - name: Build App Bundle
        run: flutter build appbundle --release

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: release-apk
          path: build/app/outputs/flutter-apk/app-release.apk

  build-ios:
    runs-on: macos-latest
    needs: test
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version-file: pubspec.yaml
          channel: stable
          cache: true

      - run: flutter pub get

      - name: Build iOS (no codesign)
        run: flutter build ios --release --no-codesign
```

> [!note] `flutter-version-file: pubspec.yaml`
> `subosito/flutter-action@v2` の v2.18.0 以降で対応。`pubspec.yaml` から Flutter バージョンを自動で読み取るため、バージョンの二重管理が不要になる。

### テスト自動化と静的解析

```yaml
# テストカバレッジの閾値チェックを追加する場合
- name: Check coverage
  run: |
    flutter test --coverage
    # lcov で最低カバレッジを検証
    sudo apt-get install lcov -y
    COVERAGE=$(lcov --summary coverage/lcov.info 2>&1 | grep "lines" | awk '{print $2}' | sed 's/%//')
    echo "Coverage: $COVERAGE%"
    if (( $(echo "$COVERAGE < 80" | bc -l) )); then
      echo "Coverage $COVERAGE% is below 80% threshold"
      exit 1
    fi
```

### Fastlane によるデプロイ自動化

#### 前提: Fastlane のインストール

```bash
# rbenv で Ruby を管理（システム Ruby は使わない）
brew install rbenv ruby-build
rbenv install 3.3.0
rbenv global 3.3.0

# Fastlane をインストール
gem install fastlane
```

#### iOS: Fastlane + Firebase App Distribution

```bash
cd ios
fastlane init
fastlane add_plugin firebase_app_distribution
```

```ruby
# ios/fastlane/Fastfile
default_platform(:ios)

platform :ios do
  desc "Firebase App Distribution にデプロイ"
  lane :distribute do
    setup_ci if ENV['CI']

    match(
      type: "adhoc",
      readonly: is_ci,
      git_url: ENV["MATCH_GIT_URL"],
      app_identifier: "com.example.myapp"
    )

    sh("flutter build ipa --release", chdir: "../")

    firebase_app_distribution(
      app: ENV["FIREBASE_APP_ID_IOS"],
      ipa_path: "../build/ios/ipa/MyApp.ipa",
      groups: "internal-testers",
      release_notes: "Build from #{git_branch}"
    )
  end
end
```

#### Android: Fastlane + Firebase App Distribution

```bash
cd android
fastlane init
fastlane add_plugin firebase_app_distribution
```

```ruby
# android/fastlane/Fastfile
default_platform(:android)

platform :android do
  desc "Firebase App Distribution にデプロイ"
  lane :distribute do
    sh("flutter build apk --release", chdir: "../")

    firebase_app_distribution(
      app: ENV["FIREBASE_APP_ID_ANDROID"],
      android_artifact_type: "APK",
      android_artifact_path: "../build/app/outputs/flutter-apk/app-release.apk",
      service_credentials_file: ENV["GOOGLE_APPLICATION_CREDENTIALS"],
      groups: "internal-testers",
      release_notes: "Build #{ENV['GITHUB_RUN_NUMBER']}"
    )
  end
end
```

#### CI 用の GitHub Secrets

| Secret 名 | 説明 |
|---|---|
| `FIREBASE_APP_ID_IOS` | Firebase iOS App ID |
| `FIREBASE_APP_ID_ANDROID` | Firebase Android App ID |
| `GOOGLE_APPLICATION_CREDENTIALS` | サービスアカウント JSON（Base64 エンコード） |
| `MATCH_GIT_URL` | 証明書管理用プライベートリポジトリの URL |
| `MATCH_PASSWORD` | Match の暗号化パスフレーズ |
| `MATCH_GIT_PRIVATE_KEY` | 証明書リポジトリアクセス用 SSH キー |
| `KEY_JKS` | Android keystore ファイル（Base64 エンコード） |

> [!warning] セキュリティ注意
> `.jks`、`.p8`、`.mobileprovision`、`google-services.json` などのファイルは **絶対にリポジトリにコミットしない**。GitHub Secrets で管理すること。

---

## 📚 11. 参考リンク・リソース

### 公式ドキュメント

- [Flutter 公式ドキュメント](https://docs.flutter.dev/)
- [Dart 公式ドキュメント](https://dart.dev/guides)
- [Flutter API リファレンス](https://api.flutter.dev/)
- [Flutter Cookbook](https://docs.flutter.dev/cookbook)
- [Pub.dev（パッケージ検索）](https://pub.dev/)

### 状態管理

- [Riverpod 公式ドキュメント](https://riverpod.dev/)
- [BLoC 公式ドキュメント](https://bloclibrary.dev/)

### ツール

- [FVM 公式ドキュメント](https://fvm.app/)
- [Fastlane 公式ドキュメント](https://docs.fastlane.tools/)
- [Firebase App Distribution](https://firebase.google.com/docs/app-distribution)

### 学習リソース

- [Code with Andrea](https://codewithandrea.com/) — Flutter のベストプラクティスと Clean Architecture
- [Flutter Community（Medium）](https://medium.com/flutter-community)
- [Flutter 公式 YouTube](https://www.youtube.com/c/flutterdev)
- [Reso Coder](https://resocoder.com/) — Clean Architecture チュートリアル

---

## ✅ セットアップチェックリスト

- [ ] macOS Sequoia 15.2 以上にアップデート
- [ ] Homebrew をインストール
- [ ] Git をインストール
- [ ] Rosetta 2 をインストール（Apple Silicon）
- [ ] FVM をインストール
- [ ] Flutter SDK（stable）をインストール
- [ ] Xcode をインストール・ライセンス承諾
- [ ] CocoaPods をインストール（フォールバック用）
- [ ] SPM を有効化
- [ ] Android Studio をインストール
- [ ] Android SDK（API 35）をインストール
- [ ] Java 17（Temurin）をインストール・設定
- [ ] Android ライセンス承諾
- [ ] エミュレータを作成（arm64-v8a）
- [ ] VS Code をインストール
- [ ] Flutter / Dart 拡張機能をインストール
- [ ] `flutter doctor` で全項目 ✓ を確認
- [ ] テストプロジェクトを作成
- [ ] iOS シミュレータで動作確認
- [ ] Android エミュレータで動作確認
- [ ] Hot Reload の動作確認
