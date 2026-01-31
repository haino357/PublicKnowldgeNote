# 7. Provider 定義例

```dart
final apiClientProvider = Provider((ref) => ApiClient());

final itemRepositoryProvider = Provider<ItemRepository>(
  (ref) => ItemRepositoryImpl(ref.read(apiClientProvider)),
);

final fetchItemsProvider = Provider(
  (ref) => FetchItems(ref.read(itemRepositoryProvider)),
);

final featureXViewModelProvider =
    StateNotifierProvider<FeatureXViewModel, AsyncValue<List<Item>>>(
  (ref) => FeatureXViewModel(ref.read(fetchItemsProvider)),
);
```
