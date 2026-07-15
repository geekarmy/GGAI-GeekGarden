# Laravel Folder Structure — Best Practices (Repository Pattern)

> Panduan struktur folder untuk project Laravel yang scalable, maintainable, dan production-ready dengan implementasi Repository Pattern.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Konvensi Laravel Default](#konvensi-laravel-default)
- [Struktur Lengkap](#struktur-lengkap)
- [Repository Pattern](#repository-pattern)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [Naming Convention](#naming-convention)
- [Best Practices](#best-practices)
- [Project Size Adaptation](#project-size-adaptation)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **Ikuti konvensi Laravel** — jangan melawan framework.
2. **Repository Pattern** — abstraksi data layer, memudahkan testing & switch DB.
3. **Fat model, thin controller** — logic di model/service, controller hanya routing.
4. **SOLID** — terutama Dependency Inversion (abstraksi via interfaces).
5. **Modular** — untuk project besar, pisahkan per domain/feature.

---

## Konvensi Laravel Default

```
laravel-project/
├── app/
│   ├── Console/        # Artisan commands
│   ├── Exceptions/     # Exception handler
│   ├── Http/           # Controllers, Middleware, Requests
│   ├── Models/         # Eloquent models
│   └── Providers/      # Service providers
├── bootstrap/
├── config/
├── database/
├── public/
├── resources/
├── routes/
├── storage/
├── tests/
├── composer.json
└── .env
```

Kita akan **nambah** folder dalam `app/` tanpa merusak konvensi di atas.

---

## Struktur Lengkap (Repository Pattern)

```
project-root/
├── app/
│   ├── Console/
│   │   ├── Commands/
│   │   │   ├── CreateAdminCommand.php
│   │   │   └── GenerateReportsCommand.php
│   │   └── Kernel.php
│   │
│   ├── Contracts/                     # Interfaces (abstraksi)
│   │   ├── Repositories/
│   │   │   ├── UserRepositoryInterface.php
│   │   │   ├── ProductRepositoryInterface.php
│   │   │   └── OrderRepositoryInterface.php
│   │   └── Services/
│   │       ├── AuthServiceInterface.php
│   │       └── PaymentServiceInterface.php
│   │
│   ├── Enums/                         # PHP 8.1+ Enums
│   │   ├── UserRole.php
│   │   ├── OrderStatus.php
│   │   └── ProductStatus.php
│   │
│   ├── Events/
│   │   ├── UserRegistered.php
│   │   └── OrderCreated.php
│   │
│   ├── Exceptions/
│   │   ├── Handler.php
│   │   └── Custom/
│   │       ├── UserNotFoundException.php
│   │       └── InsufficientStockException.php
│   │
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Api/
│   │   │   │   ├── AuthController.php
│   │   │   │   ├── UserController.php
│   │   │   │   ├── ProductController.php
│   │   │   │   └── OrderController.php
│   │   │   └── Web/
│   │   │       ├── DashboardController.php
│   │   │       └── ProfileController.php
│   │   ├── Middleware/
│   │   │   ├── ForceJsonResponse.php
│   │   │   └── LogRequests.php
│   │   └── Requests/
│   │       ├── StoreUserRequest.php
│   │       ├── UpdateUserRequest.php
│   │       ├── StoreProductRequest.php
│   │       └── LoginRequest.php
│   │
│   ├── Jobs/
│   │   ├── SendWelcomeEmail.php
│   │   └── ProcessOrder.php
│   │
│   ├── Listeners/
│   │   ├── SendEmailVerificationNotification.php
│   │   └── LogOrderCreation.php
│   │
│   ├── Mail/
│   │   ├── WelcomeMail.php
│   │   └── OrderConfirmationMail.php
│   │
│   ├── Models/
│   │   ├── User.php
│   │   ├── Product.php
│   │   ├── Order.php
│   │   └── OrderItem.php
│   │
│   ├── Notifications/
│   │   └── UserRegisteredNotification.php
│   │
│   ├── Observers/
│   │   ├── UserObserver.php
│   │   └── ProductObserver.php
│   │
│   ├── Providers/
│   │   ├── AppServiceProvider.php
│   │   ├── RepositoryServiceProvider.php  # Binding interface → implementation
│   │   └── RouteServiceProvider.php
│   │
│   ├── Repositories/                   # Implementasi repository
│   │   ├── UserRepository.php
│   │   ├── ProductRepository.php
│   │   ├── OrderRepository.php
│   │   └── BaseRepository.php          # Abstract base
│   │
│   ├── Services/                       # Business logic
│   │   ├── AuthService.php
│   │   ├── UserService.php
│   │   ├── ProductService.php
│   │   └── PaymentService.php
│   │
│   ├── Traits/
│   │   ├── ApiResponse.php
│   │   └── HasSlug.php
│   │
│   └── DTOs/                           # Data Transfer Objects (opsional)
│       ├── UserData.php
│       └── OrderData.php
│
├── bootstrap/
│
├── config/
│   ├── app.php
│   ├── database.php
│   ├── repository.php                  # Custom config repository
│   └── permission.php
│
├── database/
│   ├── factories/
│   │   ├── UserFactory.php
│   │   └── ProductFactory.php
│   ├── migrations/
│   │   ├── 0001_01_01_000000_create_users_table.php
│   │   ├── 0001_01_01_000001_create_products_table.php
│   │   └── 0001_01_01_000002_create_orders_table.php
│   └── seeders/
│       ├── DatabaseSeeder.php
│       ├── UserSeeder.php
│       └── ProductSeeder.php
│
├── public/
│   └── index.php
│
├── resources/
│   ├── views/
│   │   ├── layouts/
│   │   │   └── app.blade.php
│   │   ├── auth/
│   │   │   ├── login.blade.php
│   │   │   └── register.blade.php
│   │   ├── users/
│   │   │   └── index.blade.php
│   │   └── components/
│   │       ├── alert.blade.php
│   │       └── pagination.blade.php
│   └── lang/
│       ├── en/
│       │   ├── messages.php
│       │   └── validation.php
│       └── id/
│           ├── messages.php
│           └── validation.php
│
├── routes/
│   ├── api.php          # API routes (sanctum/passport)
│   ├── web.php          # Web routes
│   └── console.php      # Artisan routes
│
├── storage/
│   ├── app/
│   ├── logs/
│   └── framework/
│
├── tests/
│   ├── Unit/
│   │   ├── Services/
│   │   │   └── UserServiceTest.php
│   │   └── Repositories/
│   │       └── UserRepositoryTest.php
│   ├── Feature/
│   │   ├── Controllers/
│   │   │   ├── AuthControllerTest.php
│   │   │   └── UserControllerTest.php
│   │   └── Api/
│   │       └── ProductApiTest.php
│   └── TestCase.php
│
├── .env
├── .env.example
├── composer.json
└── phpunit.xml
```

---

## Repository Pattern

### Diagram Alur

```
Controller → Service (Business Logic) → Repository (Data Access) → Eloquent Model → Database
```

### Kenapa Repository Pattern?

| Alasan                  | Penjelasan |
|-------------------------|------------|
| **Abstraksi DB**        | Ganti ORM/database tanpa sentuh logic |
| **Testability**         | Mock repository saat unit test service |
| **Separation of Concern**| Controller gak perlu tahu query DB |
| **Reusable query**      | Query kompleks dipakai banyak tempat |
| **Consistent API**      | `find()`, `create()`, `update()` konsisten |

### Base Repository

```php
<?php

namespace App\Repositories;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Collection;

abstract class BaseRepository
{
    protected Model $model;

    public function __construct(Model $model)
    {
        $this->model = $model;
    }

    public function all(array $columns = ['*']): Collection
    {
        return $this->model->all($columns);
    }

    public function find(int $id): ?Model
    {
        return $this->model->find($id);
    }

    public function findOrFail(int $id): Model
    {
        return $this->model->findOrFail($id);
    }

    public function create(array $data): Model
    {
        return $this->model->create($data);
    }

    public function update(int $id, array $data): Model
    {
        $model = $this->findOrFail($id);
        $model->update($data);
        return $model->fresh();
    }

    public function delete(int $id): bool
    {
        return $this->findOrFail($id)->delete();
    }

    public function paginate(int $perPage = 15)
    {
        return $this->model->paginate($perPage);
    }

    public function findByField(string $field, mixed $value): ?Model
    {
        return $this->model->where($field, $value)->first();
    }

    public function findAllByField(string $field, mixed $value): Collection
    {
        return $this->model->where($field, $value)->get();
    }
}
```

### Interface Repository

```php
<?php

namespace App\Contracts\Repositories;

use Illuminate\Database\Eloquent\Collection;
use Illuminate\Database\Eloquent\Model;

interface UserRepositoryInterface
{
    public function all(array $columns = ['*']): Collection;
    public function find(int $id): ?Model;
    public function findOrFail(int $id): Model;
    public function create(array $data): Model;
    public function update(int $id, array $data): Model;
    public function delete(int $id): bool;
    public function findByEmail(string $email): ?Model;
    public function findActiveUsers(int $limit = 10): Collection;
}
```

### Concrete Repository

```php
<?php

namespace App\Repositories;

use App\Contracts\Repositories\UserRepositoryInterface;
use App\Models\User;
use Illuminate\Database\Eloquent\Collection;

class UserRepository extends BaseRepository implements UserRepositoryInterface
{
    public function __construct(User $user)
    {
        parent::__construct($user);
    }

    public function findByEmail(string $email): ?User
    {
        return $this->model->where('email', $email)->first();
    }

    public function findActiveUsers(int $limit = 10): Collection
    {
        return $this->model
            ->where('is_active', true)
            ->whereNull('deleted_at')
            ->latest()
            ->limit($limit)
            ->get();
    }

    public function create(array $data): User
    {
        $data['password'] = bcrypt($data['password']);
        return parent::create($data);
    }
}
```

### Binding Interface → Repository

Daftarkan binding di **RepositoryServiceProvider** agar controller/service bisa type-hint interface:

```php
<?php

namespace App\Providers;

use App\Contracts\Repositories\UserRepositoryInterface;
use App\Contracts\Repositories\ProductRepositoryInterface;
use App\Contracts\Repositories\OrderRepositoryInterface;
use App\Contracts\Services\AuthServiceInterface;
use App\Repositories\UserRepository;
use App\Repositories\ProductRepository;
use App\Repositories\OrderRepository;
use App\Services\AuthService;
use Illuminate\Support\ServiceProvider;

class RepositoryServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        // Repositories
        $this->app->bind(UserRepositoryInterface::class, UserRepository::class);
        $this->app->bind(ProductRepositoryInterface::class, ProductRepository::class);
        $this->app->bind(OrderRepositoryInterface::class, OrderRepository::class);

        // Services
        $this->app->bind(AuthServiceInterface::class, AuthService::class);
    }

    public function boot(): void
    {
        //
    }
}
```

Register di `config/app.php`:

```php
'providers' => [
    // ...
    App\Providers\RepositoryServiceProvider::class,
],
```

### Service Layer

```php
<?php

namespace App\Services;

use App\Contracts\Repositories\UserRepositoryInterface;
use App\Contracts\Services\AuthServiceInterface;
use App\DTOs\UserData;
use App\Models\User;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;

class UserService
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function getAllUsers(int $perPage = 15)
    {
        return $this->userRepository->paginate($perPage);
    }

    public function getUser(int $id): User
    {
        return $this->userRepository->findOrFail($id);
    }

    public function createUser(array $data): User
    {
        // Business logic & validasi tambahan
        if ($this->userRepository->findByEmail($data['email'])) {
            throw ValidationException::withMessages([
                'email' => 'Email already exists.',
            ]);
        }

        return $this->userRepository->create($data);
    }

    public function updateUser(int $id, array $data): User
    {
        $user = $this->userRepository->findOrFail($id);

        if (isset($data['email']) && $data['email'] !== $user->email) {
            if ($this->userRepository->findByEmail($data['email'])) {
                throw ValidationException::withMessages([
                    'email' => 'Email already taken.',
                ]);
            }
        }

        return $this->userRepository->update($id, $data);
    }

    public function deleteUser(int $id): bool
    {
        return $this->userRepository->delete($id);
    }

    public function getActiveUsers(): Collection
    {
        return $this->userRepository->findActiveUsers();
    }
}
```

### Controller

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\StoreUserRequest;
use App\Http\Requests\UpdateUserRequest;
use App\Services\UserService;
use Illuminate\Http\JsonResponse;

class UserController extends Controller
{
    public function __construct(
        private UserService $userService
    ) {}

    public function index(): JsonResponse
    {
        $users = $this->userService->getAllUsers();
        return response()->json($users);
    }

    public function store(StoreUserRequest $request): JsonResponse
    {
        $user = $this->userService->createUser($request->validated());
        return response()->json($user, 201);
    }

    public function show(int $id): JsonResponse
    {
        $user = $this->userService->getUser($id);
        return response()->json($user);
    }

    public function update(UpdateUserRequest $request, int $id): JsonResponse
    {
        $user = $this->userService->updateUser($id, $request->validated());
        return response()->json($user);
    }

    public function destroy(int $id): JsonResponse
    {
        $this->userService->deleteUser($id);
        return response()->json(null, 204);
    }
}
```

---

## Penjelasan Per Directory

### `app/Contracts/` — Interfaces

Interface untuk abstraksi repository dan service.

```
app/Contracts/
├── Repositories/
│   ├── UserRepositoryInterface.php
│   ├── ProductRepositoryInterface.php
│   └── OrderRepositoryInterface.php
└── Services/
    ├── AuthServiceInterface.php
    └── PaymentServiceInterface.php
```

**Kenapa pakai folder `Contracts/`?**
- Lebih eksplisit daripada `app/Contracts/Repositories/*Interface.php`.
- Mudah ditemukan oleh developer baru.
- Interface adalah kontrak — taruh di tempat yang jelas.

### `app/Repositories/` — Data Access Layer

Implementasi query database. Dipanggil oleh Service layer.

```
app/Repositories/
├── BaseRepository.php          # Abstract — CRUD standar
├── UserRepository.php
├── ProductRepository.php
└── OrderRepository.php
```

**Kapan query langsung di Repository vs di Model?**
- Query umum (scope, accessor, mutator) → **Model**.
- Query bisnis kompleks → **Repository**.

### `app/Services/` — Business Logic Layer

Tempat semua aturan bisnis.

```
app/Services/
├── AuthService.php
├── UserService.php
├── ProductService.php
└── PaymentService.php
```

**Aturan**:
- Service tidak tahu HTTP — hanya terima data array/DTO, return data.
- Service inject Repository via interface.
- Service return Model/Collection, bukan response JSON.

### `app/Http/Requests/` — Form Request Validation

Setiap request punya class validasi sendiri.

```
app/Http/Requests/
├── StoreUserRequest.php
├── UpdateUserRequest.php
├── StoreProductRequest.php
└── LoginRequest.php
```

```php
class StoreUserRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true; // atau gate
    }

    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|min:8|confirmed',
        ];
    }
}
```

### `app/Enums/` — PHP 8.1+ Enums

```php
<?php

namespace App\Enums;

enum OrderStatus: string
{
    case Pending    = 'pending';
    case Processing = 'processing';
    case Shipped    = 'shipped';
    case Delivered  = 'delivered';
    case Cancelled  = 'cancelled';

    public function label(): string
    {
        return match($this) {
            self::Pending    => 'Pending',
            self::Processing => 'Processing',
            self::Shipped    => 'Shipped',
            self::Delivered  => 'Delivered',
            self::Cancelled  => 'Cancelled',
        };
    }
}
```

### `app/Traits/` — Reusable Behavior

```php
<?php

namespace App\Traits;

use Illuminate\Http\JsonResponse;

trait ApiResponse
{
    protected function success(mixed $data, string $message = null, int $code = 200): JsonResponse
    {
        return response()->json([
            'status'  => 'success',
            'message' => $message,
            'data'    => $data,
        ], $code);
    }

    protected function error(string $message, int $code = 400, mixed $errors = null): JsonResponse
    {
        return response()->json([
            'status'  => 'error',
            'message' => $message,
            'errors'  => $errors,
        ], $code);
    }
}
```

### `app/DTOs/` — Data Transfer Objects

Untuk data yang melewati layer (opsional — pakai kalau suka immutable objects):

```php
<?php

namespace App\DTOs;

class UserData
{
    public function __construct(
        public readonly string $name,
        public readonly string $email,
        public readonly string $password,
        public readonly ?string $role = 'user',
    ) {}

    public static function fromRequest(array $data): self
    {
        return new self(
            name: $data['name'],
            email: $data['email'],
            password: $data['password'],
            role: $data['role'] ?? 'user',
        );
    }

    public function toArray(): array
    {
        return [
            'name'     => $this->name,
            'email'    => $this->email,
            'password' => bcrypt($this->password),
            'role'     => $this->role,
        ];
    }
}
```

### `app/Observers/` — Model Observers

Pisahkan event lifecycle model dari model itu sendiri:

```php
<?php

namespace App\Observers;

use App\Models\User;
use Illuminate\Support\Facades\Cache;

class UserObserver
{
    public function created(User $user): void
    {
        Cache::tags('users')->flush();
    }

    public function updated(User $user): void
    {
        Cache::tags('users')->flush();
    }

    public function deleted(User $user): void
    {
        Cache::tags('users')->flush();
    }
}
```

Daftarkan di `AppServiceProvider`:

```php
public function boot(): void
{
    User::observe(UserObserver::class);
    Product::observe(ProductObserver::class);
}
```

---

## Naming Convention

| Elemen                    | Convention                   | Contoh                              |
|---------------------------|------------------------------|-------------------------------------|
| Controller                | `PascalCase` + `Controller`  | `UserController.php`                |
| Service                   | `PascalCase` + `Service`     | `UserService.php`                   |
| Repository                | `PascalCase` + `Repository`  | `UserRepository.php`                |
| Interface (Repository)    | `PascalCase` + `RepositoryInterface` | `UserRepositoryInterface.php`|
| Interface (Service)       | `PascalCase` + `ServiceInterface`    | `AuthServiceInterface.php`    |
| Model                     | `PascalCase` (singuler)      | `User.php`, `OrderItem.php`         |
| Migration                 | `snake_case`                 | `2024_01_01_000001_create_users_table.php` |
| Form Request              | `PascalCase` + `Request`     | `StoreUserRequest.php`              |
| Enum                      | `PascalCase`                 | `OrderStatus.php`                   |
| Trait                     | `PascalCase`                 | `ApiResponse.php`                   |
| DTO                       | `PascalCase` + `Data`        | `UserData.php`                      |
| Mail                      | `PascalCase` + `Mail`        | `WelcomeMail.php`                   |
| Job                       | `PascalCase`                 | `SendWelcomeEmail.php`              |
| Event                     | `PascalCase` (past tense)    | `UserRegistered.php`                |
| Listener                  | `PascalCase`                 | `SendEmailVerificationNotification.php` |
| Observer                  | `PascalCase` + `Observer`    | `UserObserver.php`                  |
| Factory                   | `PascalCase` + `Factory`     | `UserFactory.php`                   |
| Seeder                    | `PascalCase` + `Seeder`      | `UserSeeder.php`                    |
| Migration file            | `snake_case`                 | `create_users_table.php`            |
| Route file                | `kebab-case`                 | `api.php`, `web.php`                |
| View file                 | `kebab-case`                 | `user-profile.blade.php`            |
| Test file                 | `PascalCase` + `Test`        | `UserServiceTest.php`               |

### Konvensi Method

| Layer       | Method Prefix            |
|-------------|--------------------------|
| Repository  | `find`, `findBy`, `findAllBy`, `create`, `update`, `delete`, `paginate` |
| Service     | `get`, `create`, `update`, `delete` (sesuai use case) |
| Controller  | `index`, `store`, `show`, `update`, `destroy` (resource) |

---

## Best Practices

### 1. Repository Pattern — jangan dipaksa untuk semua

Gunakan Repository Pattern untuk:
- Query kompleks yang dipakai banyak tempat.
- Logic yang butuh switching database (SQL → NoSQL).
- Domain yang kritikal & butuh test ketat.

**Jangan** buat repository untuk model yang pure CRUD tanpa logic tambahan — Eloquent langsung cukup.

### 2. Service layer — satu method = satu use case

```php
// ✅ Satu method = satu tanggung jawab
public function registerUser(array $data): User
public function sendVerificationEmail(User $user): void

// ❌ Jangan campur aduk
public function registerAndSendEmailAndNotifyAdmin(array $data): User
```

### 3. Controller harus tipis

```php
// ✅ Controller hanya orchestrasi
public function store(StoreUserRequest $request): JsonResponse
{
    $user = $this->userService->createUser($request->validated());
    return $this->success($user, 'User created successfully', 201);
}

// ❌ Controller penuh logic
public function store(Request $request): JsonResponse
{
    $validator = Validator::make($request->all(), [...]);
    if ($validator->fails()) { ... }
    $user = User::create([...]);
    Mail::to($user)->send(new WelcomeMail($user));
    // ... puluhan baris
}
```

### 4. Dependency Injection, jangan facade di service

```php
// ✅ DI via constructor — testable
class UserService
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}
}

// ❌ Facade di dalam service — susah mock
class UserService
{
    public function createUser(array $data): User
    {
        return User::create($data); // tightly coupled
    }
}
```

Facade di controller masih fine, tapi di service layer usahakan inject.

### 5. Form Request untuk validasi

```php
// ✅ FormRequest — validasi terpisah, rapi, reusable
// app/Http/Requests/StoreUserRequest.php

// ❌ Validasi inline di controller
public function store(Request $request)
{
    $request->validate([...]); // Controller jadi gemuk
}
```

### 6. API Resource untuk response formatting

```php
// app/Http/Resources/UserResource.php
class UserResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'         => $this->id,
            'name'       => $this->name,
            'email'      => $this->email,
            'role'       => $this->role,
            'created_at' => $this->created_at->format('Y-m-d H:i:s'),
        ];
    }
}
```

### 7. Global scope & soft delete

```php
// Model — gunakan trait bawaan Laravel
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model
{
    use SoftDeletes;

    protected $fillable = [
        'name', 'email', 'password', 'role', 'is_active',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'is_active' => 'boolean',
    ];
}
```

### 8. Query optimization di repository

```php
// Repository — eager loading, selective columns
class ProductRepository extends BaseRepository implements ProductRepositoryInterface
{
    public function findWithRelations(int $id): ?Product
    {
        return $this->model
            ->with(['category', 'variants', 'images'])
            ->withCount('reviews')
            ->find($id);
    }

    public function getActiveProductsPaginated(int $perPage = 15)
    {
        return $this->model
            ->where('is_active', true)
            ->with(['category:id,name'])
            ->select('id', 'name', 'slug', 'price', 'category_id')
            ->paginate($perPage);
    }
}
```

### 9. Gunakan `app/Traits/ApiResponse()` untuk response konsisten

```php
class BaseController extends Controller
{
    use ApiResponse;
}
```

Semua controller extends `BaseController`, response format konsisten.

### 10. Event-driven architecture untuk side effect

```php
// Controller
$user = $this->userService->createUser($data);
event(new UserRegistered($user));

// Event → Listener (bisa queue)
class SendWelcomeEmail implements ShouldQueue
{
    public function handle(UserRegistered $event): void
    {
        Mail::to($event->user)->send(new WelcomeMail($event->user));
    }
}
```

### 11. Repository testing dengan mock

```php
// tests/Unit/Services/UserServiceTest.php
class UserServiceTest extends TestCase
{
    private UserService $userService;
    private UserRepositoryInterface $userRepository;

    protected function setUp(): void
    {
        parent::setUp();
        $this->userRepository = $this->createMock(UserRepositoryInterface::class);
        $this->userService = new UserService($this->userRepository);
    }

    public function test_create_user_successfully(): void
    {
        $data = ['name' => 'John', 'email' => 'john@test.com', 'password' => 'secret'];

        $this->userRepository
            ->expects($this->once())
            ->method('findByEmail')
            ->with('john@test.com')
            ->willReturn(null);

        $this->userRepository
            ->expects($this->once())
            ->method('create')
            ->with($data)
            ->willReturn(new User($data));

        $user = $this->userService->createUser($data);
        $this->assertEquals('John', $user->name);
    }
}
```

### 12. Gunakan route grouping

```php
// routes/api.php
Route::prefix('v1')->group(function () {
    Route::post('/auth/login', [AuthController::class, 'login']);
    Route::post('/auth/register', [AuthController::class, 'register']);

    Route::middleware('auth:sanctum')->group(function () {
        Route::apiResource('users', UserController::class);
        Route::apiResource('products', ProductController::class);
        Route::apiResource('orders', OrderController::class);
    });
});
```

### 13. Query builder pattern (untuk filter kompleks)

Untuk filter dengan banyak parameter, buat class filter builder:

```php
// app/Repositories/ProductFilter.php
class ProductFilter
{
    public function __construct(
        private Builder $query
    ) {}

    public function category(?int $categoryId): self
    {
        if ($categoryId) {
            $this->query->where('category_id', $categoryId);
        }
        return $this;
    }

    public function priceRange(?float $min, ?float $max): self
    {
        if ($min) $this->query->where('price', '>=', $min);
        if ($max) $this->query->where('price', '<=', $max);
        return $this;
    }

    public function search(?string $term): self
    {
        if ($term) {
            $this->query->where(function ($q) use ($term) {
                $q->where('name', 'like', "%{$term}%")
                  ->orWhere('description', 'like', "%{$term}%");
            });
        }
        return $this;
    }

    public function sort(string $field = 'created_at', string $dir = 'desc'): self
    {
        $this->query->orderBy($field, $dir);
        return $this;
    }

    public function get(): Builder
    {
        return $this->query;
    }
}
```

### 14. Jangan abuse Repository Pattern

Repository Pattern tidak selalu dibutuhkan. Pertimbangkan:

| Situasi                          | Pakai Repository? |
|----------------------------------|-------------------|
| Model pure CRUD tanpa logic      | ❌ — Eloquent langsung |
| Query kompleks dipakai >1 tempat | ✅ |
| Unit test perlu mock data layer  | ✅ |
| Switch database (MySQL → MongoDB) | ✅ |
| API external sebagai data source | ✅ |
| Model dengan 1-2 query sederhana  | ❌ — overhead |

### 15. Package recommendation

| Kebutuhan           | Package                          |
|---------------------|----------------------------------|
| Repository generator| `laravelista/lumen-repository`   |
| Query filter        | `spatie/laravel-query-builder`   |
| DTO                 | `spatie/data-transfer-object`    |
| API Resources       | Built-in Laravel API Resources   |
| Auth API            | `laravel/sanctum`                |
| Enum               | Built-in PHP 8.1 Enum           |
| Media               | `spatie/laravel-medialibrary`    |
| Permission          | `spatie/laravel-permission`      |

---

## Project Size Adaptation

| Ukuran Project         | Struktur |
|------------------------|----------|
| **Kecil** (CRUD)       | Laravel default + `app/Services/` |
| **Sedang** (5-15 model)| Laravel + `Repositories/` + `Services/` + `Contracts/` |
| **Besar** (monolith)   | Modular — pecah per domain/feature |

### Modular Laravel (Project Besar)

```
app/
├── Modules/
│   ├── User/
│   │   ├── Controllers/
│   │   ├── Requests/
│   │   ├── Services/
│   │   ├── Repositories/
│   │   ├── Models/
│   │   ├── Resources/
│   │   ├── routes.php
│   │   └── Providers/
│   │       └── UserServiceProvider.php
│   ├── Product/
│   │   └── ...
│   └── Order/
│       └── ...
├── Http/
├── Providers/
└── ...
```

Atau gunakan package modular seperti **nWidart/laravel-modules**.

---

## Referensi

- [Laravel Documentation](https://laravel.com/docs)
- [Laravel API Resources](https://laravel.com/docs/eloquent-resources)
- [Laravel Form Requests](https://laravel.com/docs/validation#form-request-validation)
- [Laravel Service Providers](https://laravel.com/docs/providers)
- [Repository Pattern in Laravel (Laravel News)](https://laravel-news.com/repository-pattern-in-laravel)
- [Spatie Laravel Packages](https://spatie.be/docs/laravel)
- [PHP 8.1 Enums](https://www.php.net/manual/en/language.types.enumerations.php)

---

> **Catatan**: Repository Pattern adalah alat, bukan dogma. Gunakan saat memberi nilai tambah (testability, abstraction). Untuk project kecil, Eloquent langsung tanpa repository lebih efisien. Sesuaikan dengan skala dan kebutuhan tim.
