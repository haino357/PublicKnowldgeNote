# 8. API 接続とデータアクセス

## 8-1. Base URL と Dio の用意
環境ごとに baseUrl を切り替えられるようにしておく（Flavor / dotenv など）。

```dart
// core/utils/app_config.dart
class AppConfig {
  static const baseUrl = String.fromEnvironment(
    'BASE_URL',
    defaultValue: 'https://api.example.com',
  );
}

// app/di/providers.dart
final dioProvider = Provider((ref) {
  final dio = Dio(
    BaseOptions(
      baseUrl: AppConfig.baseUrl,
      connectTimeout: const Duration(seconds: 10),
      receiveTimeout: const Duration(seconds: 10),
    ),
  );
  dio.interceptors.add(
    LogInterceptor(requestBody: true, responseBody: true),
  );
  return dio;
});

final apiClientProvider = Provider((ref) => ApiClient(ref.read(dioProvider)));
```

## 8-2. API クライアント（薄いラッパー）
```dart
// core/network/api_client.dart
class ApiClient {
  ApiClient(this._dio);
  final Dio _dio;

  Future<Response<T>> get<T>(
    String path, {
    Map<String, dynamic>? queryParameters,
  }) {
    return _dio.get<T>(path, queryParameters: queryParameters);
  }
}
```

## 8-3. Remote DataSource
```dart
// data/feature_x/datasources/item_remote_data_source.dart
abstract class ItemRemoteDataSource {
  Future<List<ItemDto>> fetchItems();
}

class ItemRemoteDataSourceImpl implements ItemRemoteDataSource {
  ItemRemoteDataSourceImpl(this._client);
  final ApiClient _client;

  @override
  Future<List<ItemDto>> fetchItems() async {
    final response = await _client.get<List<dynamic>>('/items');
    final data = response.data ?? <dynamic>[];
    return data
        .map((e) => ItemDto.fromJson(e as Map<String, dynamic>))
        .toList();
  }
}
```

## 8-4. Repository Impl（DTO → Entity 変換）
```dart
// data/feature_x/repositories/item_repository_impl.dart
class ItemRepositoryImpl implements ItemRepository {
  ItemRepositoryImpl(this._remote);
  final ItemRemoteDataSource _remote;

  @override
  Future<List<Item>> fetchItems() async {
    final dtos = await _remote.fetchItems();
    return dtos.map((e) => e.toEntity()).toList();
  }
}
```

## 8-5. DTO → Entity
```dart
// data/feature_x/models/item_dto.dart
class ItemDto {
  ItemDto({required this.id, required this.title});

  final String id;
  final String title;

  factory ItemDto.fromJson(Map<String, dynamic> json) {
    return ItemDto(
      id: json['id'] as String,
      title: json['title'] as String,
    );
  }

  Item toEntity() => Item(id: id, title: title);
}
```

## 8-6. Provider の接続
```dart
final itemRemoteDataSourceProvider = Provider<ItemRemoteDataSource>(
  (ref) => ItemRemoteDataSourceImpl(ref.read(apiClientProvider)),
);

final itemRepositoryProvider = Provider<ItemRepository>(
  (ref) => ItemRepositoryImpl(ref.read(itemRemoteDataSourceProvider)),
);
```

## 8-7. エラーハンドリングの置き場所
- **Data 層**: DioException → Exception に変換  
- **Domain 層**: Exception → Failure などの抽象化  
- **Presentation**: Failure を UI 表示に変換

## 8-8. Failure / Exception の最小実装例
```dart
// core/error/exceptions.dart
class ServerException implements Exception {
  ServerException(this.message, {this.statusCode});
  final String message;
  final int? statusCode;
}

class NetworkException implements Exception {
  NetworkException(this.message);
  final String message;
}

// core/error/failures.dart
abstract class Failure {
  const Failure(this.message);
  final String message;
}

class ServerFailure extends Failure {
  const ServerFailure(super.message);
}

class NetworkFailure extends Failure {
  const NetworkFailure(super.message);
}
```

```dart
// data/feature_x/repositories/item_repository_impl.dart
class ItemRepositoryImpl implements ItemRepository {
  ItemRepositoryImpl(this._remote);
  final ItemRemoteDataSource _remote;

  @override
  Future<List<Item>> fetchItems() async {
    try {
      final dtos = await _remote.fetchItems();
      return dtos.map((e) => e.toEntity()).toList();
    } on ServerException catch (e) {
      throw ServerFailure('Server error: ${e.message}');
    } on NetworkException catch (e) {
      throw NetworkFailure('Network error: ${e.message}');
    }
  }
}
```

```dart
// data/feature_x/datasources/item_remote_data_source.dart
class ItemRemoteDataSourceImpl implements ItemRemoteDataSource {
  ItemRemoteDataSourceImpl(this._client);
  final ApiClient _client;

  @override
  Future<List<ItemDto>> fetchItems() async {
    try {
      final response = await _client.get<List<dynamic>>('/items');
      final data = response.data ?? <dynamic>[];
      return data
          .map((e) => ItemDto.fromJson(e as Map<String, dynamic>))
          .toList();
    } on DioException catch (e) {
      if (e.type == DioExceptionType.connectionError) {
        throw NetworkException(e.message ?? 'connection error');
      }
      throw ServerException(
        e.message ?? 'server error',
        statusCode: e.response?.statusCode,
      );
    }
  }
}
```

## 8-9. LocalDataSource + Cache の実装パターン
基本方針は **Repository がキャッシュ戦略を決める**。DataSource は単純に読み書きのみ。

### 8-9-1. Local DataSource
```dart
// data/feature_x/datasources/item_local_data_source.dart
abstract class ItemLocalDataSource {
  Future<List<ItemDto>> fetchItems();
  Future<void> cacheItems(List<ItemDto> items);
  Future<void> clear();
  Future<DateTime?> lastUpdatedAt();
}

class ItemLocalDataSourceImpl implements ItemLocalDataSource {
  ItemLocalDataSourceImpl(this._store);
  final KeyValueStore _store; // SharedPreferences/Hive などの薄いラッパー

  static const _itemsKey = 'items_cache';
  static const _updatedAtKey = 'items_cache_updated_at';

  @override
  Future<List<ItemDto>> fetchItems() async {
    final raw = await _store.getStringList(_itemsKey);
    if (raw == null) {
      throw CacheException('cache not found');
    }
    return raw
        .map((e) => ItemDto.fromJson(_store.decodeJson(e)))
        .toList();
  }

  @override
  Future<void> cacheItems(List<ItemDto> items) async {
    final raw = items.map(_store.encodeJson).toList();
    await _store.setStringList(_itemsKey, raw);
    await _store.setString(_updatedAtKey, DateTime.now().toIso8601String());
  }

  @override
  Future<void> clear() async {
    await _store.remove(_itemsKey);
    await _store.remove(_updatedAtKey);
  }

  @override
  Future<DateTime?> lastUpdatedAt() async {
    final value = await _store.getString(_updatedAtKey);
    return value == null ? null : DateTime.tryParse(value);
  }
}
```

### 8-9-2. Repository でキャッシュ戦略を制御
```dart
// data/feature_x/repositories/item_repository_impl.dart
class ItemRepositoryImpl implements ItemRepository {
  ItemRepositoryImpl(this._remote, this._local);
  final ItemRemoteDataSource _remote;
  final ItemLocalDataSource _local;

  @override
  Future<List<Item>> fetchItems({bool forceRefresh = false}) async {
    if (!forceRefresh) {
      final cached = await _tryReadCache();
      if (cached != null) return cached;
    }

    final dtos = await _remote.fetchItems();
    await _local.cacheItems(dtos);
    return dtos.map((e) => e.toEntity()).toList();
  }

  Future<List<Item>?> _tryReadCache() async {
    try {
      final updatedAt = await _local.lastUpdatedAt();
      final isFresh = updatedAt != null &&
          DateTime.now().difference(updatedAt) < const Duration(minutes: 15);
      if (!isFresh) return null;

      final dtos = await _local.fetchItems();
      return dtos.map((e) => e.toEntity()).toList();
    } on CacheException {
      return null;
    }
  }
}
```

### 8-9-3. Provider 追加例
```dart
final keyValueStoreProvider = Provider<KeyValueStore>(
  (ref) => SharedPrefsStore(), // 実体はアプリに合わせて差し替え
);

final itemLocalDataSourceProvider = Provider<ItemLocalDataSource>(
  (ref) => ItemLocalDataSourceImpl(ref.read(keyValueStoreProvider)),
);
```

## 8-10. Result/Either で Failure を返すパターン
例として簡易 Result を用意し、Repository が Failure を返す構成。

### 8-10-1. Result 型
```dart
// core/utils/result.dart
sealed class Result<T> {
  const Result();
}

class Success<T> extends Result<T> {
  const Success(this.value);
  final T value;
}

class FailureResult<T> extends Result<T> {
  const FailureResult(this.failure);
  final Failure failure;
}
```

### 8-10-2. Repository（Result を返す）
```dart
// domain/feature_x/repositories/item_repository.dart
abstract class ItemRepository {
  Future<Result<List<Item>>> fetchItems();
}
```

```dart
// data/feature_x/repositories/item_repository_impl.dart
class ItemRepositoryImpl implements ItemRepository {
  ItemRepositoryImpl(this._remote);
  final ItemRemoteDataSource _remote;

  @override
  Future<Result<List<Item>>> fetchItems() async {
    try {
      final dtos = await _remote.fetchItems();
      final items = dtos.map((e) => e.toEntity()).toList();
      return Success(items);
    } on ServerException catch (e) {
      return FailureResult(ServerFailure('Server error: ${e.message}'));
    } on NetworkException catch (e) {
      return FailureResult(NetworkFailure('Network error: ${e.message}'));
    }
  }
}
```

### 8-10-3. UseCase（Result をそのまま返す）
```dart
// domain/feature_x/usecases/fetch_items.dart
class FetchItems {
  FetchItems(this._repo);
  final ItemRepository _repo;

  Future<Result<List<Item>>> call() {
    return _repo.fetchItems();
  }
}
```

## 8-11. ViewModel で Failure を UI 表示に変換する例
### 8-11-1. 表示用の UIState
```dart
// presentation/feature_x/viewmodel/feature_x_state.dart
class FeatureXState {
  const FeatureXState({
    this.isLoading = false,
    this.items = const [],
    this.errorMessage,
  });

  final bool isLoading;
  final List<Item> items;
  final String? errorMessage;

  FeatureXState copyWith({
    bool? isLoading,
    List<Item>? items,
    String? errorMessage,
  }) {
    return FeatureXState(
      isLoading: isLoading ?? this.isLoading,
      items: items ?? this.items,
      errorMessage: errorMessage,
    );
  }
}
```

### 8-11-2. Failure → UI 表示変換
```dart
// presentation/feature_x/viewmodel/feature_x_viewmodel.dart
class FeatureXViewModel extends StateNotifier<FeatureXState> {
  FeatureXViewModel(this._fetchItems) : super(const FeatureXState());
  final FetchItems _fetchItems;

  Future<void> load() async {
    state = state.copyWith(isLoading: true, errorMessage: null);

    final result = await _fetchItems();
    switch (result) {
      case Success<List<Item>>(:final value):
        state = state.copyWith(isLoading: false, items: value);
      case FailureResult<List<Item>>(:final failure):
        state = state.copyWith(
          isLoading: false,
          errorMessage: _mapFailureToMessage(failure),
        );
    }
  }

  String _mapFailureToMessage(Failure failure) {
    return switch (failure) {
      NetworkFailure() => 'ネットワークに接続できませんでした',
      ServerFailure() => 'サーバーで問題が発生しました',
      _ => '不明なエラーが発生しました',
    };
  }
}
```

### 8-11-3. View 側の表示
```dart
// presentation/feature_x/view/feature_x_page.dart
class FeatureXPage extends ConsumerWidget {
  const FeatureXPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final state = ref.watch(featureXViewModelProvider);

    if (state.isLoading) {
      return const Center(child: CircularProgressIndicator());
    }

    if (state.errorMessage != null) {
      return Center(child: Text(state.errorMessage!));
    }

    return ListView(
      children: state.items.map((e) => Text(e.title)).toList(),
    );
  }
}
```
