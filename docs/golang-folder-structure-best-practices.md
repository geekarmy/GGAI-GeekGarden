# Go Project Folder Structure — Best Practices

> Panduan struktur folder untuk project Go yang scalable, idiomatic, dan production-ready.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Go Workspace & Module](#go-workspace--module)
- [Struktur Lengkap](#struktur-lengkap)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [Internal vs Pkg](#internal-vs-pkg)
- [Layered Architecture](#layered-architecture)
- [Clean Architecture](#clean-architecture)
- [Naming Convention](#naming-convention)
- [Best Practices](#best-practices)
- [Project Size Adaptation](#project-size-adaptation)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **Idiomatic Go** — ikuti standar `go build`, `go test`, `go mod`.
2. **`internal` untuk privasi** — package `internal/` tidak bisa di-import dari luar.
3. **`cmd` sebagai entry point** — binary `main.go` di `cmd/`, bukan di root.
4. **Jangan over-engineer** — mulai simple, tambah folder saat benar-benar dibutuhkan.
5. **Zero or few global state** — gunakan dependency injection via struct/konstruktor.

---

## Go Workspace & Module

### Single Module (kebanyakan project)

```
project-root/
├── go.mod
├── go.sum
├── main.go              # ❌ Sebaiknya di cmd/
└── ...
```

### Multi-Binary Module

```
project-root/
├── cmd/
│   ├── api/
│   │   └── main.go      # ✅ Binary API server
│   └── worker/
│       └── main.go      # ✅ Binary background worker
├── pkg/
├── internal/
├── go.mod
└── go.sum
```

### Go Workspace (multi-module)

```
project-root/
├── go.work               # Workspace file
├── service-a/
│   ├── go.mod
│   └── cmd/
├── service-b/
│   ├── go.mod
│   └── cmd/
└── libs/
    ├── go.mod
    └── ...
```

---

## Struktur Lengkap

```
project-root/
├── cmd/
│   ├── api/                          # HTTP/gRPC server
│   │   └── main.go
│   ├── worker/                       # Background worker
│   │   └── main.go
│   └── cli/                          # CLI tool
│       └── main.go
│
├── internal/                         # Private packages (tidak bisa di-import luar)
│   ├── api/
│   │   ├── handler/
│   │   │   ├── auth_handler.go
│   │   │   ├── user_handler.go
│   │   │   ├── product_handler.go
│   │   │   └── middleware.go
│   │   ├── router.go
│   │   └── response.go
│   │
│   ├── service/                      # Business logic / use cases
│   │   ├── auth_service.go
│   │   ├── user_service.go
│   │   └── product_service.go
│   │
│   ├── repository/                   # Data access layer
│   │   ├── user_repository.go
│   │   ├── product_repository.go
│   │   └── db.go                     # DB connection / transaction
│   │
│   ├── model/                        # Domain models / entities
│   │   ├── user.go
│   │   ├── product.go
│   │   └── order.go
│   │
│   ├── dto/                          # Data transfer objects (request/response)
│   │   ├── user_dto.go
│   │   └── common.go
│   │
│   ├── config/                       # App configuration
│   │   └── config.go
│   │
│   └── validator/                    # Custom validators
│       └── validator.go
│
├── pkg/                              # Public packages (bisa di-import project lain)
│   ├── logger/
│   │   └── logger.go
│   ├── pagination/
│   │   └── pagination.go
│   ├── jwt/
│   │   └── jwt.go
│   └── utils/
│       ├── hash.go
│       └── string.go
│
├── api/                              # API definitions (protobuf, OpenAPI)
│   ├── proto/
│   │   ├── auth.proto
│   │   └── user.proto
│   ├── openapi/
│   │   └── spec.yaml
│   └── graphql/
│       └── schema.graphql
│
├── migrations/                       # Database migrations
│   ├── 000001_create_users.up.sql
│   ├── 000001_create_users.down.sql
│   ├── 000002_create_products.up.sql
│   └── 000002_create_products.down.sql
│
├── scripts/                          # Build & utility scripts
│   ├── migrate.sh
│   ├── seed.sh
│   └── docker-entrypoint.sh
│
├── configs/                          # Configuration files (YAML, TOML, JSON)
│   ├── config.yaml
│   ├── config.dev.yaml
│   └── config.prod.yaml
│
├── deploy/                           # Deployment manifests
│   ├── Dockerfile
│   ├── docker-compose.yaml
│   └── k8s/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── configmap.yaml
│
├── docs/                             # Dokumentasi
│   └── architecture.md
│
├── test/                             # E2E / integration test helpers
│   ├── testutil/
│   │   ├── db.go                     # Test database setup
│   │   └── fixture.go
│   └── integration_test.go
│
├── web/                              # Web assets (jika backend serve frontend)
│   ├── static/
│   ├── template/
│   │   ├── index.html
│   │   └── layout.html
│   └── embed.go
│
├── .github/                          # CI/CD
│   └── workflows/
│       ├── ci.yaml
│       └── release.yaml
│
├── .gitignore
├── .golangci.yml                     # Linter config
├── go.mod
├── go.sum
├── Makefile
└── README.md
```

---

## Penjelasan Per Directory

### `cmd/` — Application Entry Points

Root dari setiap binary yang dihasilkan.

```
cmd/
├── api/                   → go build -o bin/api ./cmd/api
│   └── main.go            → Inisialisasi dependencies, start server
├── worker/
│   └── main.go
└── cli/
    └── main.go
```

**Aturan**:
- `main.go` di `cmd/` minimal — hanya parsing config, inject dependencies, start.
- Satu folder per binary.
- Jangan taruh business logic di `cmd/`.

```go
// cmd/api/main.go
package main

import (
  "log"
  "your-project/internal/api"
  "your-project/internal/config"
)

func main() {
  cfg := config.Load()
  server := api.NewServer(cfg)
  log.Fatal(server.Start())
}
```

### `internal/` — Private Application Packages

Package yang **tidak bisa di-import** dari luar module. Ini adalah enkapsulasi paksa Go.

```
internal/
├── api/          # HTTP layer: handler, middleware, router
├── service/      # Business logic / use cases
├── repository/   # Data access (SQL, Redis, external API)
├── model/        # Domain entities
├── dto/          # Request/response structs
├── config/       # Config loader
└── validator/    # Validation logic
```

**Keuntungan `internal/`**:
- Compiler-enforced encapsulation.
- Dependency graph lebih jelas.
- Refactor internal tanpa takut breaking external consumers.

### `pkg/` — Public Reusable Packages

Package yang **sengaja dibuat publik** untuk di-import project lain.

```
pkg/
├── logger/        → import "your-project/pkg/logger"
├── pagination/    → import "your-project/pkg/pagination"
├── jwt/           → import "your-project/pkg/jwt"
└── utils/         → import "your-project/pkg/utils"
```

**Kriteria masuk `pkg/`**:
- Utility murni (tidak tergantung business logic).
- Reusable di luar konteks project.
- Punya API yang stable dan documented.

**Jangan** taruh di `pkg/` kalau hanya dipakai internal — taruh di `internal/`.

### `api/` — API Contracts

Definisi API yang menjadi kontrak antara service/client.

| Sub-folder    | Tools Populer          |
|---------------|------------------------|
| `api/proto/`  | Protocol Buffers (gRPC)|
| `api/openapi/`| OpenAPI / Swagger spec |
| `api/graphql/`| GraphQL schema         |

**Best practice**: Generate Go code dari spec → simpan generated code di `internal/api/gen/`.

### `migrations/` — Database Migrations

SQL migration files — satu file per perubahan skema.

```
migrations/
├── 000001_create_users.up.sql
├── 000001_create_users.down.sql
├── 000002_add_email_to_users.up.sql
└── 000002_add_email_to_users.down.sql
```

Gunakan tool:
- **golang-migrate/migrate** — CLI + library
- **pressly/goose** — Go-based migration tool

### `configs/` — Configuration Files

Konfigurasi environment-specific (YAML, TOML, JSON).

```
configs/
├── config.yaml          # Default
├── config.dev.yaml      # Development override
└── config.prod.yaml     # Production override
```

Di Go, biasanya config dibaca dari env variable atau file YAML:

```go
// internal/config/config.go
type Config struct {
  Server   ServerConfig   `yaml:"server"`
  Database DatabaseConfig `yaml:"database"`
}

type ServerConfig struct {
  Port    int    `yaml:"port"`
  Timeout int    `yaml:"timeout"`
}

func Load() Config {
  var cfg Config
  viper.Unmarshal(&cfg) // atau envconfig, koanf, dll
  return cfg
}
```

---

## Internal vs Pkg

| Aspek           | `internal/`                          | `pkg/`                              |
|-----------------|--------------------------------------|-------------------------------------|
| Visibility      | Hanya module ini                     | Public — siapapun bisa import       |
| Use case        | Business logic, handler, repository  | Utility, shared libraries           |
| Refactor        | Bebas ubah kapan saja                | Breaking change = semver major      |
| Dokumentasi     | Minim                                | Harus documented dengan baik        |
| Testing         | Test langsung                        | Test + compatibility test           |

**Golden rule**: Taruh di `internal/` dulu. Jika benar-benar perlu di-import project lain, pindahkan ke `pkg/`.

---

## Layered Architecture

Struktur layered standar untuk backend Go:

```
handler (api)  →  service  →  repository
     │               │              │
     │               │              └── database / external API
     │               │
     │               └── business logic, validasi, orchestrasi
     │
     └── HTTP handling, parsing request, formatting response
```

### Dependency Injection via Constructor

```go
// internal/api/handler/user_handler.go
type UserHandler struct {
  userService *service.UserService
}

func NewUserHandler(userService *service.UserService) *UserHandler {
  return &UserHandler{userService: userService}
}

func (h *UserHandler) GetUser(w http.ResponseWriter, r *http.Request) {
  id := chi.URLParam(r, "id")
  user, err := h.userService.GetByID(r.Context(), id)
  if err != nil {
    http.Error(w, err.Error(), http.StatusNotFound)
    return
  }
  json.NewEncoder(w).Encode(user)
}
```

```go
// internal/service/user_service.go
type UserRepository interface {
  FindByID(ctx context.Context, id string) (*model.User, error)
}

type UserService struct {
  repo UserRepository
}

func NewUserService(repo UserRepository) *UserService {
  return &UserService{repo: repo}
}

func (s *UserService) GetByID(ctx context.Context, id string) (*model.User, error) {
  // Business logic: validasi, transformasi, authorization
  if err := validateUUID(id); err != nil {
    return nil, ErrInvalidID
  }
  return s.repo.FindByID(ctx, id)
}
```

### Wiring di `cmd/api/main.go`

```go
func main() {
  cfg := config.Load()
  db := repository.NewDB(cfg.Database)
  
  userRepo := repository.NewUserRepository(db)
  userSvc := service.NewUserService(userRepo)
  userHandler := handler.NewUserHandler(userSvc)

  server := api.NewServer(cfg.Server, userHandler)
  server.Start()
}
```

---

## Clean Architecture

Untuk project kompleks, adopsi Clean Architecture (Robert C. Martin):

```
internal/
├── entity/              # Enterprise business rules
│   ├── user.go
│   └── product.go
│
├── usecase/             # Application business rules
│   ├── user_usecase.go
│   └── product_usecase.go
│
├── repository/          # Interface definition
│   └── interfaces.go
│
├── handler/             # Delivery mechanism (HTTP/gRPC/CLI)
│   └── user_handler.go
│
└── infra/               # Implementasi repository (DB, API eksternal)
    ├── postgres/
    │   └── user_repo.go
    └── redis/
        └── cache.go
```

### Dependency Rule

```
entity → usecase → repository (interface)
                      ↓
                    infra (implementation)
```

**Tidak ada dependency dari layer luar ke layer dalam** — pakai interfaces untuk invert dependency.

```go
// internal/usecase/user_usecase.go
type UserRepository interface {
  FindByID(ctx context.Context, id string) (*entity.User, error)
}

type UserUsecase struct {
  repo UserRepository
}

func (uc *UserUsecase) GetUser(ctx context.Context, id string) (*entity.User, error) {
  return uc.repo.FindByID(ctx, id)
}
```

```go
// internal/infra/postgres/user_repo.go
type UserRepoPostgres struct {
  db *sql.DB
}

func (r *UserRepoPostgres) FindByID(ctx context.Context, id string) (*entity.User, error) {
  // Query PostgreSQL
}
```

---

## Naming Convention

| Elemen                 | Convention                | Contoh                        |
|------------------------|---------------------------|-------------------------------|
| File & folder          | `snake_case`              | `user_handler.go`             |
| Package name           | `lowercase`, one word     | `package handler`             |
| Exported function      | `PascalCase`              | `func NewServer()`            |
| Unexported function    | `camelCase`               | `func parseConfig()`          |
| Interface name         | `-er` suffix / `PascalCase` | `Reader`, `UserRepository` |
| Struct                 | `PascalCase`              | `type UserService struct`     |
| Variable               | `camelCase`               | `userRepo`, `dbConn`          |
| Constant               | `PascalCase`              | `MaxRetryCount`               |
| Error variable         | `Err` prefix              | `ErrNotFound`                 |
| Test file              | `*_test.go`               | `user_service_test.go`        |
| Mock file              | `*_mock.go`               | `user_repo_mock.go`           |
| Migration file         | `NNNNNNNN_*.sql`          | `000001_create_users.up.sql`  |

### File Naming Tips

| Tipe File       | Nama File                   |
|-----------------|-----------------------------|
| HTTP handler    | `user_handler.go`           |
| Service         | `auth_service.go`           |
| Repository      | `product_repository.go`     |
| Entity/model    | `user.go` (tanpa suffix)    |
| Config          | `config.go`                 |
| Middleware      | `middleware.go`             |
| Router          | `router.go`                 |
| DTO             | `user_dto.go`               |

---

## Best Practices

### 1. `main.go` harus minimal

```go
// ❌ Jangan taruh business logic di main.go
func main() {
  http.HandleFunc("/users", func(w http.ResponseWriter, r *http.Request) { ... })
  log.Fatal(http.ListenAndServe(":8080", nil))
}

// ✅ Main hanya wiring
func main() {
  cfg := config.Load()
  srv := api.NewServer(cfg)
  srv.RegisterRoutes()
  srv.Start()
}
```

### 2. Gunakan interfaces untuk testability

```go
// internal/repository/user_repository.go
type UserRepository interface {
  FindByID(ctx context.Context, id string) (*model.User, error)
  Create(ctx context.Context, user *model.User) error
}
```

Implementasi konkret:

```go
// internal/repository/postgres/user_repo.go
type userRepoPostgres struct {
  db *sql.DB
}

func NewUserRepository(db *sql.DB) UserRepository {
  return &userRepoPostgres{db: db}
}
```

Mock untuk testing:

```go
// internal/repository/user_repo_mock.go
type UserRepositoryMock struct {
  FindByIDFn func(ctx context.Context, id string) (*model.User, error)
}

func (m *UserRepositoryMock) FindByID(ctx context.Context, id string) (*model.User, error) {
  return m.FindByIDFn(ctx, id)
}
```

### 3. Pakai `internal/` untuk enkapsulasi — jangan andalkan disiplin tim

```go
// ❌ Semua package bisa di-import dari luar — rawan misuse
// pkg/service/user_service.go

// ✅ Compiler mencegah import dari luar
// internal/service/user_service.go
```

### 4. Struct-based dependency injection, jangan global state

```go
// ❌ Global variable — susah di-test
var db *sql.DB

// ✅ Struct-based — mudah mock & test
type UserService struct {
  repo UserRepository
}
```

### 5. Error handling — gunakan sentinel errors + wrapping

```go
// internal/model/errors.go
var (
  ErrNotFound = errors.New("not found")
  ErrConflict = errors.New("already exists")
)

// internal/service/user_service.go
if err != nil {
  return fmt.Errorf("find user: %w", ErrNotFound)
}

// Caller
if errors.Is(err, ErrNotFound) {
  // Handle 404
}
```

### 6. Context propagation — oper ctx ke semua layer

```go
func (s *UserService) GetByID(ctx context.Context, id string) (*model.User, error) {
  // ctx membawa trace ID, timeout, cancellation
  return s.repo.FindByID(ctx, id)
}
```

### 7. Test structure

```
internal/service/
├── user_service.go
├── user_service_test.go          # ✅ Colocated unit test
└── user_service_integration_test.go  # Integration test (build tag)
```

Gunakan build tags untuk integration test:

```go
// user_service_integration_test.go
//go:build integration

package service

func TestUserServiceIntegration(t *testing.T) {
  // Test dengan database nyata
}
```

Run: `go test -tags=integration ./...`

### 8. `go generate` untuk boilerplate

```go
//go:generate mockgen -source=user_repository.go -destination=user_repo_mock.go -package=repository
//go:generate sqlc generate
```

### 9. Linter — wajib `golangci-lint`

```yaml
# .golangci.yml
linters:
  enable:
    - errcheck
    - gosimple
    - govet
    - ineffassign
    - staticcheck
    - unused
    - revive
    - gofmt
    - bodyclose
```

### 10. Graceful shutdown

```go
// cmd/api/main.go
func main() {
  srv := api.NewServer(cfg)

  go func() {
    if err := srv.Start(); err != nil && err != http.ErrServerClosed {
      log.Fatal(err)
    }
  }()

  // Wait for interrupt
  quit := make(chan os.Signal, 1)
  signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
  <-quit

  ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
  defer cancel()
  srv.Shutdown(ctx)
}
```

### 11. Env-based configuration

Gunakan library seperti `caarlos0/env`, `kelseyhightower/envconfig`, atau `spf13/viper`:

```go
// internal/config/config.go
type Config struct {
  Port     int    `env:"PORT" envDefault:"8080"`
  DBURL    string `env:"DATABASE_URL" envDefault:"postgres://localhost:5432"`
  LogLevel string `env:"LOG_LEVEL" envDefault:"info"`
}

func Load() Config {
  var cfg Config
  if err := env.Parse(&cfg); err != nil {
    log.Fatal(err)
  }
  return cfg
}
```

### 12. Jangan gunakan `init()` untuk business logic

```go
// ❌ init() menyulitkan testing
func init() {
  db = connectDB()
}

// ✅ Eksplisit via constructor
func NewServer(cfg Config) *Server {
  db := connectDB(cfg.DatabaseURL)
  return &Server{db: db}
}
```

### 13. Group related test cases

```go
func TestUserService(t *testing.T) {
  t.Run("GetByID - found", func(t *testing.T) { ... })
  t.Run("GetByID - not found", func(t *testing.T) { ... })
  t.Run("Create - success", func(t *testing.T) { ... })
  t.Run("Create - duplicate email", func(t *testing.T) { ... })
}
```

### 14. Jangan export struct fields kalau tidak perlu

```go
type UserService struct {
  repo UserRepository // unexported — tidak bisa diakses dari luar
}

func NewUserService(repo UserRepository) *UserService {
  return &UserService{repo: repo}
}
```

### 15. SQLC atau GORM?

| Kebutuhan            | Recomendasi    |
|----------------------|----------------|
| Type safety maksimal | **sqlc** — generate Go code dari SQL |
| Full ORM features    | **GORM** — cepat prototyping |
| Query builder        | **sqlx** + **squirrel** — balance type safety + flexibilitas |
| Complex query        | **sqlc** — raw SQL full control |

---

## Project Size Adaptation

| Ukuran Project       | Struktur                                    |
|----------------------|---------------------------------------------|
| **Kecil** (1-2 binary) | `cmd/`, `internal/` — flat, tanpa layer     |
| **Sedang** (3-5 binary) | `cmd/`, `internal/{handler,service,repository,model}`, `pkg/` |
| **Besar** (monorepo, microservices) | Go workspace + clean architecture + `api/` protobuf |

### Starter (Kecil)

```
myapp/
├── cmd/api/main.go
├── internal/
│   ├── handler.go
│   ├── service.go
│   └── repository.go
├── go.mod
└── Makefile
```

### Medium (Standar)

```
myapp/
├── cmd/api/main.go
├── internal/
│   ├── api/handler/
│   ├── service/
│   ├── repository/
│   └── model/
├── pkg/logger/
├── migrations/
├── configs/
├── go.mod
└── Makefile
```

### Large (Clean Architecture)

```
myapp/
├── cmd/
│   ├── api/main.go
│   └── worker/main.go
├── internal/
│   ├── entity/
│   ├── usecase/
│   ├── handler/
│   ├── repository/
│   └── infra/
│       ├── postgres/
│       └── redis/
├── pkg/
│   ├── logger/
│   └── jwt/
├── api/proto/
├── migrations/
├── deploy/
├── go.mod
├── go.work
└── Makefile
```

---

## Referensi

- [Go Standard Project Layout](https://github.com/golang-standards/project-layout)
- [Go Blog — Organizing Go Code](https://go.dev/blog/organizing-go-code)
- [Go Wiki — Go Test Comments](https://github.com/golang/go/wiki/TestComments)
- [Clean Architecture in Go](https://manuel.kiessling.net/2012/09/28/applying-the-clean-web-architecture-to-go/)
- [Practical Go: Real world advice](https://dave.cheney.net/practical-go/presentations/qcon-china.html)
- [golangci-lint](https://golangci-lint.run/)

---

> **Catatan**: Go community tidak punya satu "official" struktur folder. Struktur di atas adalah konsensus dari project production-grade. Mulai simple, tambah kompleksitas seiring kebutuhan. Yang paling penting: `internal/`, `cmd/`, dan package naming yang konsisten.
