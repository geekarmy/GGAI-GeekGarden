# Flutter Folder Structure — Best Practices

> Panduan struktur folder untuk project Flutter yang scalable, maintainable, dan production-ready.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Flutter Default Structure](#flutter-default-structure)
- [Struktur Lengkap](#struktur-lengkap)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [Feature-Based Structure](#feature-based-structure)
- [Clean Architecture di Flutter](#clean-architecture-di-flutter)
- [State Management & Struktur](#state-management--struktur)
- [Naming Convention](#naming-convention)
- [Best Practices](#best-practices)
- [Project Size Adaptation](#project-size-adaptation)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **Feature-based** — kumpulkan file per fitur, bukan per tipe.
2. **Separation of concern** — pisahkan UI, logic, data, model.
3. **Widget-tree friendly** — satu file = satu widget, jangan raksasa.
4. **State management choice** — pilih satu, konsisten (Riverpod/BLoC/Provider).
5. **Testable** — pisahkan business logic dari framework widget.

---

## Flutter Default Structure

```
flutter-project/
├── android/
├── ios/
├── lib/
│   └── main.dart
├── test/
│   └── widget_test.dart
├── pubspec.yaml
└── ...
```

Struktur yang akan kita bangun ada di dalam `lib/`.

---

## Struktur Lengkap (Feature-Based + Clean Architecture)

```
project-root/
├── android/
├── ios/
├── assets/
│   ├── fonts/
│   │   └── inter.ttf
│   ├── images/
│   │   ├── logo.png
│   │   └── illustrations/
│   │       └── empty-state.svg
│   ├── icons/
│   │   └── app-icon.svg
│   ├── l10n/                        # Localization (ARB files)
│   │   ├── app_en.arb
│   │   └── app_id.arb
│   └── jsons/                       # Mock JSON / assets data
│       └── products.json
│
├── lib/
│   ├── main.dart                    # Entry point
│   ├── app.dart                     # MaterialApp widget + routing + theme
│   │
│   ├── config/                      # App configuration
│   │   ├── app_config.dart
│   │   ├── env_config.dart
│   │   └── theme/
│   │       ├── app_theme.dart
│   │       ├── app_colors.dart
│   │       ├── app_text_styles.dart
│   │       └── app_dimensions.dart
│   │
│   ├── routes/                      # Route definitions
│   │   ├── app_router.dart
│   │   ├── route_names.dart
│   │   └── route_guards.dart
│   │
│   ├── core/                        # Shared infrastructure
│   │   ├── constants/
│   │   │   ├── api_constants.dart
│   │   │   └── app_constants.dart
│   │   ├── errors/
│   │   │   ├── failures.dart
│   │   │   └── exceptions.dart
│   │   ├── network/
│   │   │   ├── api_client.dart
│   │   │   ├── api_interceptors.dart
│   │   │   ├── api_response.dart
│   │   │   └── network_info.dart
│   │   ├── utils/
│   │   │   ├── validators.dart
│   │   │   ├── formatters.dart
│   │   │   ├── extensions.dart
│   │   │   └── debouncer.dart
│   │   ├── widgets/                 # Shared UI components
│   │   │   ├── app_button.dart
│   │   │   ├── app_text_field.dart
│   │   │   ├── app_card.dart
│   │   │   ├── loading_widget.dart
│   │   │   ├── error_widget.dart
│   │   │   ├── empty_state_widget.dart
│   │   │   └── app_shell.dart       # Bottom nav / scaffold wrapper
│   │   └── localization/
│   │       └── app_localizations.dart
│   │
│   ├── features/                    # Feature modules
│   │   ├── auth/
│   │   │   ├── data/
│   │   │   │   ├── datasources/
│   │   │   │   │   ├── auth_remote_datasource.dart
│   │   │   │   │   └── auth_local_datasource.dart
│   │   │   │   ├── models/
│   │   │   │   │   ├── user_model.dart
│   │   │   │   │   └── token_model.dart
│   │   │   │   └── repositories/
│   │   │   │       └── auth_repository_impl.dart
│   │   │   ├── domain/
│   │   │   │   ├── entities/
│   │   │   │   │   └── user.dart
│   │   │   │   ├── repositories/
│   │   │   │   │   └── auth_repository.dart      # Interface
│   │   │   │   └── usecases/
│   │   │   │       ├── login_usecase.dart
│   │   │   │       ├── register_usecase.dart
│   │   │   │       └── logout_usecase.dart
│   │   │   └── presentation/
│   │   │       ├── providers/                    # Riverpod/Provider
│   │   │       │   ├── auth_provider.dart
│   │   │       │   └── auth_state.dart
│   │   │       ├── bloc/                         # BLoC pattern
│   │   │       │   ├── auth_bloc.dart
│   │   │       │   ├── auth_event.dart
│   │   │       │   └── auth_state.dart
│   │   │       └── pages/
│   │   │           ├── login_page.dart
│   │   │           ├── register_page.dart
│   │   │           └── widgets/
│   │   │               ├── login_form.dart
│   │   │               └── social_login_buttons.dart
│   │   │
│   │   ├── products/
│   │   │   ├── data/
│   │   │   │   ├── datasources/
│   │   │   │   │   └── product_remote_datasource.dart
│   │   │   │   ├── models/
│   │   │   │   │   └── product_model.dart
│   │   │   │   └── repositories/
│   │   │   │       └── product_repository_impl.dart
│   │   │   ├── domain/
│   │   │   │   ├── entities/
│   │   │   │   │   └── product.dart
│   │   │   │   ├── repositories/
│   │   │   │   │   └── product_repository.dart
│   │   │   │   └── usecases/
│   │   │   │       ├── get_products_usecase.dart
│   │   │   │       └── get_product_detail_usecase.dart
│   │   │   └── presentation/
│   │   │       ├── providers/
│   │   │       │   ├── product_provider.dart
│   │   │       │   └── product_state.dart
│   │   │       └── pages/
│   │   │           ├── product_list_page.dart
│   │   │           ├── product_detail_page.dart
│   │   │           └── widgets/
│   │   │               ├── product_card.dart
│   │   │               └── product_grid.dart
│   │   │
│   │   └── orders/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   │
│   └── di/                           # Dependency injection
│       └── injection_container.dart   # GetIt / Riverpod providers
│
├── test/
│   ├── unit/
│   │   ├── features/
│   │   │   ├── auth/
│   │   │   │   ├── domain/usecases/
│   │   │   │   │   └── login_usecase_test.dart
│   │   │   │   └── data/repositories/
│   │   │   │       └── auth_repository_impl_test.dart
│   │   │   └── products/
│   │   │       └── ...
│   │   └── core/
│   │       └── utils/
│   │           └── validators_test.dart
│   ├── widget/
│   │   ├── core/widgets/
│   │   │   └── app_button_test.dart
│   │   └── features/
│   │       └── auth/
│   │           └── login_page_test.dart
│   ├── integration/
│   │   └── auth_flow_test.dart
│   └── helpers/
│       ├── test_data.dart
│       └── mock_providers.dart
│
├── integration_test/
│   └── app_test.dart
│
├── web/                              # Web-specific files
├── windows/
├── macos/
├── linux/
│
├── .env                              # Environment variables
├── .env.example
├── .gitignore
├── analysis_options.yaml
├── pubspec.yaml
├── pubspec.lock
└── README.md
```

---

## Penjelasan Per Directory

### `lib/main.dart` — Entry Point

```dart
import 'package:flutter/material.dart';
import 'app.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await initializeDependencies();  // GetIt, Hive, dll
  runApp(const App());
}
```

### `lib/app.dart` — Root Widget

```dart
class App extends StatelessWidget {
  const App({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      title: 'GGAI App',
      theme: AppTheme.light,
      darkTheme: AppTheme.dark,
      routerConfig: AppRouter.router,
      localizationsDelegates: AppLocalizations.localizationsDelegates,
      supportedLocales: AppLocalizations.supportedLocales,
    );
  }
}
```

### `lib/config/` — App Configuration

| File                | Fungsi |
|---------------------|--------|
| `app_config.dart`   | App metadata (nama, versi, base URL) |
| `env_config.dart`   | Env-based config (dev/staging/prod) |
| `theme/`            | Tema: colors, text styles, spacing |

```dart
// config/app_config.dart
class AppConfig {
  static const String appName = 'GGAI';
  static const String baseUrl = String.fromEnvironment('API_BASE_URL');
  static const int pageSize = 20;
}
```

### `lib/routes/` — Routing

Gunakan GoRouter untuk declarative routing:

```dart
// routes/app_router.dart
import 'package:go_router/go_router.dart';

final goRouter = GoRouter(
  initialLocation: '/login',
  routes: [
    GoRoute(
      path: '/login',
      name: RouteNames.login,
      builder: (context, state) => const LoginPage(),
    ),
    GoRoute(
      path: '/products',
      name: RouteNames.products,
      builder: (context, state) => const ProductListPage(),
      routes: [
        GoRoute(
          path: ':id',
          name: RouteNames.productDetail,
          builder: (context, state) => ProductDetailPage(
            id: state.pathParameters['id']!,
          ),
        ),
      ],
    ),
  ],
);
```

### `lib/core/` — Shared Infrastructure

Kode yang **tidak terikat** fitur tertentu.

| Sub-folder      | Fungsi |
|-----------------|--------|
| `constants/`    | API base URL, endpoint paths, app constants |
| `errors/`       | `Failure`, `ServerException`, `CacheException` |
| `network/`      | Dio client, interceptors, network info checker |
| `utils/`        | Validators, formatters, date helpers, extensions |
| `widgets/`      | Shared UI components (Button, TextField, Card, Loading) |
| `localization/` | i18n setup |

#### Core Widgets Suggestion

```
core/widgets/
├── app_button.dart
├── app_text_field.dart
├── app_card.dart
├── app_dialog.dart
├── app_snackbar.dart
├── loading_widget.dart
├── error_widget.dart
├── empty_state_widget.dart
├── shimmer_loading.dart
└── app_bottom_sheet.dart
```

### `lib/features/` — Feature Modules

Setiap fitur terdiri dari 3 layer (Clean Architecture):

```
feature/
├── data/            # Data layer
│   ├── datasources/ # Remote (API) & Local (DB/Preferences)
│   ├── models/      # Data model (JSON serialization)
│   └── repositories/# Implementasi repository interface
├── domain/          # Domain layer
│   ├── entities/    # Business objects (pure Dart)
│   ├── repositories/# Interface repository
│   └── usecases/    # Business logic / Use cases
└── presentation/    # UI layer
    ├── providers/   # State management (Riverpod / BLoC)
    └── pages/       # Screens + widgets
```

#### Data Layer

```dart
// data/datasources/product_remote_datasource.dart
class ProductRemoteDataSource {
  final Dio dio;

  ProductRemoteDataSource(this.dio);

  Future<List<ProductModel>> getProducts(int page) async {
    final response = await dio.get('/products', queryParameters: {'page': page});
    return (response.data['data'] as List)
        .map((json) => ProductModel.fromJson(json))
        .toList();
  }
}
```

```dart
// data/models/product_model.dart
class ProductModel extends Product {
  const ProductModel({
    required super.id,
    required super.name,
    required super.price,
  });

  factory ProductModel.fromJson(Map<String, dynamic> json) => ProductModel(
        id: json['id'] as String,
        name: json['name'] as String,
        price: (json['price'] as num).toDouble(),
      );

  Map<String, dynamic> toJson() => {
        'id': id,
        'name': name,
        'price': price,
      };
}
```

#### Domain Layer

```dart
// domain/entities/product.dart
class Product {
  final String id;
  final String name;
  final double price;

  const Product({
    required this.id,
    required this.name,
    required this.price,
  });
}
```

```dart
// domain/usecases/get_products_usecase.dart
class GetProductsUseCase {
  final ProductRepository repository;

  GetProductsUseCase(this.repository);

  Future<Either<Failure, List<Product>>> call(int page) {
    return repository.getProducts(page);
  }
}
```

#### Presentation Layer

```dart
// presentation/providers/product_provider.dart
@riverpod
class ProductList extends _$ProductList {
  @override
  Future<List<Product>> build(int page) async {
    final useCase = ref.watch(getProductsUseCaseProvider);
    return useCase(page).then((either) => either.fold(
          (failure) => throw failure,
          (products) => products,
        ));
  }
}
```

### `lib/di/` — Dependency Injection

Gunakan `GetIt` + injectable:

```dart
// di/injection_container.dart
final sl = GetIt.instance;

Future<void> initializeDependencies() async {
  // External
  sl.registerLazySingleton<Dio>(() => createDioClient());

  // Core
  sl.registerLazySingleton<NetworkInfo>(() => NetworkInfoImpl());

  // Data sources
  sl.registerLazySingleton<ProductRemoteDataSource>(
    () => ProductRemoteDataSource(sl()),
  );

  // Repositories
  sl.registerLazySingleton<ProductRepository>(
    () => ProductRepositoryImpl(sl()),
  );

  // Use cases
  sl.registerLazySingleton(() => GetProductsUseCase(sl()));

  // BLoC / Providers
  sl.registerFactory(() => ProductBloc(sl()));
}
```

---

## Feature-Based Structure

### Perbandingan: Type-Based vs Feature-Based

```
// ❌ Type-based — cari file susah, scaling buruk
lib/
├── models/
│   ├── user.dart
│   ├── product.dart
│   └── order.dart
├── screens/
│   ├── login_screen.dart
│   ├── product_list_screen.dart
│   └── product_detail_screen.dart
├── widgets/
│   ├── custom_button.dart
│   ├── product_card.dart
│   └── loading_indicator.dart
└── services/
    ├── auth_service.dart
    ├── api_service.dart
    └── product_service.dart

// ✅ Feature-based — cohesion tinggi, scaling mudah
lib/
├── features/
│   ├── auth/
│   ├── products/
│   └── orders/
├── core/
│   └── widgets/   # Hanya shared widgets
```

### Feature Dependency Rule

```
presentation → domain ← data
                    ↑
              (interface)
```

- **Presentation** depends on **domain** (entities + use cases).
- **Data** depends on **domain** (implements repository interface).
- **Domain** has zero dependency on Flutter framework — pure Dart.

---

## Clean Architecture di Flutter

### Layer Diagram

```
┌──────────────────────────────┐
│   Presentation (UI)          │
│   - Pages / Screens         │
│   - Widgets                 │
│   - Providers / BLoC        │
├──────────────────────────────┤
│   Domain (Business Logic)   │
│   - Entities                │
│   - Use Cases               │
│   - Repository Interfaces   │  ← Pure Dart, no framework
├──────────────────────────────┤
│   Data (Implementation)     │
│   - Repository Impl         │
│   - Data Sources (API/DB)   │
│   - Models (DTO)            │
└──────────────────────────────┘
```

### Folder Structure per Layer

| Layer        | Folder              | Dependencies             |
|--------------|----------------------|--------------------------|
| Presentation | `presentation/`      | Flutter, domain          |
| Domain       | `domain/`            | Pure Dart (no Flutter)   |
| Data         | `data/`              | Domain, packages (Dio)   |

**Keuntungan Clean Architecture di Flutter**:
- **Testable** — domain layer bisa di-test tanpa Flutter.
- **Swap implementation** — ganti API → GraphQL tanpa sentuh domain.
- **Separation** — UI engineer gak perlu pusing logic.

---

## State Management & Struktur

### Riverpod (Recommended)

```
auth/presentation/
├── providers/
│   ├── auth_provider.dart          # StateNotifierProvider / AsyncNotifier
│   ├── auth_state.dart             # Freezed state class
│   └── auth_notifier.dart          # Business logic
└── pages/
    ├── login_page.dart
    └── widgets/
        └── login_form.dart
```

Contoh:

```dart
// providers/auth_state.dart
@freezed
class AuthState with _$AuthState {
  const factory AuthState.initial() = _Initial;
  const factory AuthState.loading() = _Loading;
  const factory AuthState.authenticated(User user) = _Authenticated;
  const factory AuthState.error(String message) = _Error;
}

// providers/auth_notifier.dart
class AuthNotifier extends StateNotifier<AuthState> {
  final LoginUseCase loginUseCase;

  AuthNotifier(this.loginUseCase) : super(const AuthState.initial());

  Future<void> login(String email, String password) async {
    state = const AuthState.loading();
    final result = await loginUseCase(LoginParams(email, password));
    result.fold(
      (failure) => state = AuthState.error(failure.message),
      (user) => state = AuthState.authenticated(user),
    );
  }
}
```

### BLoC

```
auth/presentation/
├── bloc/
│   ├── auth_bloc.dart
│   ├── auth_event.dart
│   └── auth_state.dart
└── pages/
    ├── login_page.dart
    └── widgets/
        └── login_form.dart
```

### Decision: Riverpod vs BLoC vs Provider

| Kebutuhan               | Riverpod | BLoC | Provider |
|-------------------------|----------|------|----------|
| Simple state            | ✅       | ❌   | ✅       |
| Complex async           | ✅       | ✅   | ⚠️       |
| Testability             | ✅       | ✅   | ⚠️       |
| Boilerplate             | Low      | High | Medium   |
| Compile-safe            | ✅       | ⚠️   | ❌       |
| Learning curve          | Medium   | High | Low      |

---

## Naming Convention

| Elemen                    | Convention            | Contoh                            |
|---------------------------|-----------------------|-----------------------------------|
| Project name              | `snake_case`          | `ggai_app`                        |
| File & folder             | `snake_case`          | `login_page.dart`                 |
| Class                     | `PascalCase`          | `LoginPage`                       |
| Widget                    | `PascalCase`          | `ProductCard`                     |
| Function/method           | `camelCase`           | `fetchProducts()`                 |
| Variable                  | `camelCase`           | `productList`                     |
| Constant                  | `lowerCamelCase`      | `defaultPageSize`                 |
| Enum                      | `PascalCase`          | `enum OrderStatus`                |
| Enum value                | `lowerCamelCase`      | `OrderStatus.pending`             |
| Mixin                     | `PascalCase`          | `mixin ValidationMixin`           |
| Extension                 | `on + PascalCase`     | `extension StringExtension`       |
| Provider (Riverpod)       | `camelCase`           | `productListProvider`             |
| BLoC                      | `PascalCase` + `Bloc` | `AuthBloc`                        |
| Use case                  | `PascalCase` + `UseCase` | `LoginUseCase`                 |
| Event (BLoC)              | `PascalCase` + `Event`   | `LoginSubmittedEvent`          |
| State (BLoC)              | `PascalCase` + `State`   | `AuthAuthenticatedState`       |
| Test file                 | `*_test.dart`         | `login_page_test.dart`            |
| Model file                | `*_model.dart`        | `user_model.dart`                 |
| Datasource file           | `*_datasource.dart`   | `auth_remote_datasource.dart`     |
| Repository file           | `*_repository.dart`   | `auth_repository.dart`            |
| Repository impl           | `*_repository_impl.dart` | `auth_repository_impl.dart`    |

---

## Best Practices

### 1. Satu widget = satu file

```dart
// ❌ Jangan gabung banyak widget dalam satu file besar
// widgets.dart — 500 lines

// ✅ Satu class per file
// core/widgets/app_button.dart
// core/widgets/app_text_field.dart
// features/products/presentation/widgets/product_card.dart
```

### 2. Gunakan `const` constructor semaksimal mungkin

```dart
// ✅ Widget rebuild lebih efisien
class ProductCard extends StatelessWidget {
  const ProductCard({super.key, required this.product});

  // ❌ Jangan:
  // ProductCard({Key? key, required this.product}) : super(key: key);
}
```

### 3. Pisahkan UI dan Business Logic

```dart
// ✅ Presentasi hanya render state
class LoginPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final authState = ref.watch(authProvider);
    return authState.when(
      loading: () => const LoadingWidget(),
      error: (msg) => ErrorWidget(message: msg),
      authenticated: (_) => const DashboardPage(),
      initial: () => const LoginForm(),
    );
  }
}

// ❌ Jangan taruh API call langsung di widget
```

### 4. Gunakan Dart code generation

| Package          | Fungsi                      |
|------------------|-----------------------------|
| `freezed`        | State class, union types    |
| `json_serializable` | JSON serialization      |
| `build_runner`   | Code generation runner      |
| `injectable`     | GetIt DI generator          |
| `riverpod_generator` | Riverpod code generation|

### 5. DI via constructor, jangan global singleton

```dart
// ✅ DI via constructor — testable
class GetProductsUseCase {
  final ProductRepository repository;
  GetProductsUseCase(this.repository);
}

// ❌ Global singleton — susah mocking
class GetProductsUseCase {
  final repo = GetIt.instance<ProductRepository>();
}
```

### 6. Error handling — gunakan Either pattern

```dart
// ✅ Pakai dartz Either atau Result
Either<Failure, List<Product>> getProducts(int page);

// ❌ Throw exception mentah
List<Product> getProducts(int page);
```

### 7. File naming konsisten dengan class

```dart
// ✅ Nama file = snake_case dari class
// class LoginPage → login_page.dart
// class LoginForm → login_form.dart

// ❌ Jangan:
// login_page.dart → class LoginScreen (beda nama)
```

### 8. Gunakan `part` dan `part of` untuk generated code

```dart
// user_model.dart
import 'package:json_annotation/json_annotation.dart';

part 'user_model.g.dart';

@JsonSerializable()
class UserModel {
  final String id;
  final String email;

  UserModel({required this.id, required this.email});

  factory UserModel.fromJson(Map<String, dynamic> json) =>
      _$UserModelFromJson(json);
  Map<String, dynamic> toJson() => _$UserModelToJson(this);
}
```

### 9. Theme terpusat — jangan hardcode

```dart
// ❌ Hardcode — susah maintain
Text(
  'Hello',
  style: TextStyle(fontSize: 16, color: Colors.blue),
);

// ✅ Theme terpusat
Text(
  'Hello',
  style: Theme.of(context).textTheme.bodyLarge?.copyWith(
        color: Theme.of(context).colorScheme.primary,
      ),
);
```

### 10. Jangan kirim context ke ViewModel/Bloc

```dart
// ❌ Jangan passing BuildContext ke BLoC
class AuthBloc {
  void login(BuildContext context, String email, String pw) {
    Navigator.push(context, MaterialPageRoute(...));
  }
}

// ✅ Navigator di UI layer
class LoginPage {
  void _onLoginSuccess(BuildContext context) {
    context.go('/dashboard');
  }
}
```

### 11. Localization — gunakan ARB files

```
assets/l10n/
├── app_en.arb
└── app_id.arb
```

```json
// app_en.arb
{
  "@@locale": "en",
  "appTitle": "GGAI App",
  "login": "Login",
  "email": "Email"
}
```

Generate dengan `flutter gen-l10n`.

### 12. Asset management — grouping folder

```
assets/
├── fonts/
├── images/           # PNG, JPG
├── icons/            # SVG icons
├── illustrations/    # SVG illustrations
└── l10n/             # ARB files
```

### 13. Path alias — gunakan package

```yaml
# pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  # ...

flutter:
  assets:
    - assets/images/
```

Atau gunakan package seperti `path_provider` + custom path helper.

### 14. Test colocation

```
features/auth/
├── data/repositories/
│   ├── auth_repository_impl.dart
│   └── auth_repository_impl_test.dart    ✅ Colocated
├── domain/usecases/
│   ├── login_usecase.dart
│   └── login_usecase_test.dart           ✅ Colocated
└── presentation/pages/
    ├── login_page.dart
    └── login_page_test.dart              ✅ Colocated
```

Atau flat di `/test/` — konsisten dengan struktur `lib/`:

```
test/
├── unit/
│   └── features/auth/
│       ├── domain/usecases/login_usecase_test.dart
│       └── data/repositories/auth_repository_impl_test.dart
├── widget/
│   └── features/auth/login_page_test.dart
└── integration/
    └── auth_flow_test.dart
```

### 15. Environment-based config

```dart
// config/env_config.dart
class EnvConfig {
  final String apiBaseUrl;
  final bool useHttps;
  final LogLevel logLevel;

  const EnvConfig._({
    required this.apiBaseUrl,
    required this.useHttps,
    required this.logLevel,
  });

  static const EnvConfig dev = EnvConfig._(
    apiBaseUrl: 'http://localhost:3000/api',
    useHttps: false,
    logLevel: LogLevel.debug,
  );

  static const EnvConfig prod = EnvConfig._(
    apiBaseUrl: 'https://api.ggai.app',
    useHttps: true,
    logLevel: LogLevel.info,
  );
}
```

### 16. Package recommendations

| Kebutuhan           | Package                               |
|---------------------|---------------------------------------|
| State management    | `riverpod` / `flutter_bloc`           |
| Routing             | `go_router`                           |
| Networking          | `dio`                                 |
| Code generation     | `freezed` + `json_serializable`       |
| DI                  | `get_it` + `injectable`               |
| Local DB            | `drift` (SQLite) / `hive` / `isar`    |
| Localization        | `flutter_localizations` (built-in)    |
| Caching             | `dio_cache_interceptor`               |
| Image loading       | `cached_network_image`                |
| SVG                 | `flutter_svg`                         |
| Functional          | `dartz` (Either) / `fpdart`           |
| Testing             | `mocktail` / `bloc_test`              |
| Validation          | `formz`                               |
| Permissions         | `permission_handler`                  |

---

## Project Size Adaptation

| Ukuran Project         | Struktur |
|------------------------|----------|
| **Kecil** (1-5 screens) | Flat — semua di `lib/` + `lib/widgets/` |
| **Sedang** (5-15 features) | Feature-based tanpa Clean Architecture layers |
| **Besar** (15+ features) | Feature-based + Clean Architecture + code generation |

### Starter (Kecil)

```
lib/
├── main.dart
├── app.dart
├── config/
│   └── theme.dart
├── models/
├── pages/
├── widgets/
├── services/
└── utils/
```

### Medium

```
lib/
├── main.dart
├── app.dart
├── config/
├── core/
│   └── widgets/
├── features/
│   ├── auth/
│   │   ├── providers/
│   │   ├── pages/
│   │   └── widgets/
│   ├── products/
│   │   ├── providers/
│   │   └── pages/
│   └── orders/
└── di/
```

### Large (Clean Architecture)

Seperti struktur lengkap di atas — setiap feature punya `data/`, `domain/`, `presentation/`.

---

## Referensi

- [Flutter Official Docs — Layout](https://docs.flutter.dev/ui/layout)
- [Flutter Official Docs — State Management](https://docs.flutter.dev/data-and-backend/state-mgmt)
- [Clean Architecture — Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Reso Coder — Clean Architecture Flutter](https://resocoder.com/flutter-clean-architecture)
- [Riverpod Documentation](https://riverpod.dev)
- [BLoC Library](https://bloclibrary.dev)
- [Flutter Design Patterns](https://flutter.dev/docs/resources/design-patterns)
- [Very Good Ventures — Flutter Architecture](https://verygood.ventures/blog/flutter-app-architecture)

---

> **Catatan**: Pilih state management yang sesuai tim, bukan yang paling populer. Riverpod untuk tim kecil/sedang. BLoC untuk tim besar dengan strict pattern. Jangan copy-paste struktur mentah — adaptasi dengan kompleksitas project.
