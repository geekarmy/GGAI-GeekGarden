# NestJS Folder Structure — Best Practices

> Panduan struktur folder untuk project NestJS yang scalable, maintainable, dan production-ready.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Modular Structure](#modular-structure)
- [Struktur Lengkap](#struktur-lengkap)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [Feature Module (Atomic Design)](#feature-module-atomic-design)
- [Shared Module](#shared-module)
- [Common / Core Module](#common--core-module)
- [Naming Convention](#naming-convention)
- [Best Practices](#best-practices)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **Modular** — setiap fitur adalah module mandiri.
2. **Separation of Concern** — pisahkan controller, service, repository, dll.
3. **Domain-driven** — struktur mengikuti domain bisnis, bukan teknis.
4. **Scalable** — mudah ditambah fitur baru tanpa mengganggu yang lain.
5. **Testable** — dependency injection memudahkan mocking.

---

## Modular Structure

```
src/
├── common/          # Shared utilities, guards, interceptors, filters
├── config/          # Konfigurasi environment
├── modules/         # Feature modules (domains)
│   ├── auth/
│   ├── users/
│   ├── products/
│   └── orders/
└── main.ts          # Entry point
```

---

## Struktur Lengkap

```
project-root/
├── src/
│   ├── common/
│   │   ├── constants/
│   │   │   └── index.ts
│   │   ├── decorators/
│   │   │   ├── current-user.decorator.ts
│   │   │   ├── public.decorator.ts
│   │   │   └── roles.decorator.ts
│   │   ├── dto/
│   │   │   ├── pagination.dto.ts
│   │   │   └── api-response.dto.ts
│   │   ├── filters/
│   │   │   ├── http-exception.filter.ts
│   │   │   └── all-exceptions.filter.ts
│   │   ├── guards/
│   │   │   ├── jwt-auth.guard.ts
│   │   │   └── roles.guard.ts
│   │   ├── interceptors/
│   │   │   ├── logging.interceptor.ts
│   │   │   ├── transform.interceptor.ts
│   │   │   └── timeout.interceptor.ts
│   │   ├── middleware/
│   │   │   └── logger.middleware.ts
│   │   ├── pipes/
│   │   │   ├── validation.pipe.ts
│   │   │   └── parse-uuid.pipe.ts
│   │   └── common.module.ts
│   │
│   ├── config/
│   │   ├── app.config.ts
│   │   ├── database.config.ts
│   │   ├── jwt.config.ts
│   │   ├── config.module.ts
│   │   └── env.validation.ts
│   │
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.resolver.ts          # (jika pakai GraphQL)
│   │   │   ├── strategies/
│   │   │   │   ├── jwt.strategy.ts
│   │   │   │   └── local.strategy.ts
│   │   │   ├── dto/
│   │   │   │   ├── login.dto.ts
│   │   │   │   └── register.dto.ts
│   │   │   ├── interfaces/
│   │   │   │   └── auth.interface.ts
│   │   │   └── __tests__/
│   │   │       ├── auth.controller.spec.ts
│   │   │       └── auth.service.spec.ts
│   │   │
│   │   ├── users/
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── entities/
│   │   │   │   └── user.entity.ts
│   │   │   ├── dto/
│   │   │   │   ├── create-user.dto.ts
│   │   │   │   ├── update-user.dto.ts
│   │   │   │   └── user-response.dto.ts
│   │   │   ├── repositories/
│   │   │   │   └── user.repository.ts
│   │   │   ├── interfaces/
│   │   │   │   └── user.interface.ts
│   │   │   ├── subscribers/
│   │   │   │   └── user.subscriber.ts
│   │   │   └── __tests__/
│   │   │       ├── users.controller.spec.ts
│   │   │       └── users.service.spec.ts
│   │   │
│   │   ├── products/
│   │   │   ├── products.module.ts
│   │   │   ├── products.controller.ts
│   │   │   ├── products.service.ts
│   │   │   ├── entities/
│   │   │   │   └── product.entity.ts
│   │   │   ├── dto/
│   │   │   │   ├── create-product.dto.ts
│   │   │   │   └── update-product.dto.ts
│   │   │   ├── repositories/
│   │   │   │   └── product.repository.ts
│   │   │   └── __tests__/
│   │   │
│   │   └── orders/
│   │       ├── orders.module.ts
│   │       ├── orders.controller.ts
│   │       ├── orders.service.ts
│   │       ├── entities/
│   │       │   ├── order.entity.ts
│   │       │   └── order-item.entity.ts
│   │       ├── dto/
│   │       │   ├── create-order.dto.ts
│   │       │   └── update-order.dto.ts
│   │       ├── repositories/
│   │       │   └── order.repository.ts
│   │       └── __tests__/
│   │
│   ├── database/
│   │   ├── migrations/
│   │   ├── seeds/
│   │   │   ├── user.seed.ts
│   │   │   └── product.seed.ts
│   │   └── data-source.ts
│   │
│   ├── app.module.ts
│   └── main.ts
│
├── docs/                    # Dokumentasi project
├── scripts/                 # Utility scripts
├── uploads/                 # File uploads (gitignored)
├── test/                    # E2E tests
│   ├── jest-e2e.json
│   ├── app.e2e-spec.ts
│   └── auth.e2e-spec.ts
│
├── .env
├── .env.example
├── .eslintrc.js
├── .prettierrc
├── nest-cli.json
├── tsconfig.json
├── tsconfig.build.json
├── package.json
└── README.md
```

---

## Penjelasan Per Directory

### `src/common/` — Shared Cross-Cutting Concerns

Berisi kode yang digunakan oleh banyak module secara global.

| Sub-folder      | Fungsi |
|-----------------|--------|
| `constants/`    | Enum, konstanta global |
| `decorators/`   | Custom decorators (e.g., `@CurrentUser`, `@Public`) |
| `dto/`          | Global/shared DTO (e.g., pagination, API response wrapper) |
| `filters/`      | Exception filters — handle error formatting |
| `guards/`       | Auth & role guards |
| `interceptors/` | Request/response transformation, logging, timeout |
| `middleware/`   | Express middleware (logger, cors, helmet) |
| `pipes/`        | Custom validation pipes |

Register semua provider di `common.module.ts`, lalu import sekali di `AppModule`.

### `src/config/` — Environment Configuration

- Gunakan `@nestjs/config` + `joi` / `zod` untuk validasi env.
- Satu file per domain konfigurasi (`app`, `database`, `jwt`, dll).
- `config.module.ts` — ConfigModule global.
- `env.validation.ts` — skema validasi environment variables.

### `src/modules/` — Feature Modules (Domain)

> **Setiap fitur bisnis = satu folder module.**

Struktur internal per module:

```
auth/
├── auth.module.ts        # Deklarasi module + imports/exports
├── auth.controller.ts    # REST endpoints (atau *.resolver.ts untuk GraphQL)
├── auth.service.ts       # Business logic
├── strategies/           # Passport strategies (jika pakai auth)
├── dto/                  # Request/response DTO spesifik module
├── interfaces/           # TypeScript interfaces/types
├── entities/             # TypeORM / Prisma entity models
├── repositories/         # Custom repository (jika perlu)
├── subscribers/          # Entity subscribers / lifecycle hooks
└── __tests__/            # Unit tests (module-level)
```

#### Kapan pakai `entities/` vs `repositories/` vs `interfaces/`?

| Folder          | ORM-based (TypeORM/MikroORM) | Prisma | Mongoose |
|----------------|------------------------------|--------|----------|
| `entities/`    | ✅ Entity dekorator          | ❌ (schema di `schema.prisma`) | ❌ (schema terpisah) |
| `repositories/`| ✅ Custom repository         | ❌ (pake PrismaService langsung) | ❌ (pake Model mongoose) |
| `interfaces/`  | Type/interface murni         | ✅ Generated types dari Prisma | ✅ Interface untuk document |

### `src/database/` — Database Migrations & Seeds

- `migrations/` — Auto-generated migration files.
- `seeds/` — Data seeder untuk development.
- `data-source.ts` — TypeORM `DataSource` config untuk CLI.

### `test/` — E2E Tests

- `jest-e2e.json` — Jest config untuk e2e.
- Gunakan `supertest` + in-memory database atau test container.

---

## Feature Module (Atomic Design)

Setiap module di `src/modules/` bersifat **self-contained**:

1. **Module** — deklarasikan controller, service, dan exports.
2. **Controller** — handle routing + validasi input.
3. **Service** — business logic, orchestrasi.
4. **Repository / ORM** — akses database.
5. **Entity / Schema** — model definition.
6. **DTO** — validasi input/output.
7. **Interfaces** — TypeScript types.

**Rule of thumb**: Jika kode hanya dipakai oleh satu module, taruh di dalam module itu. Jika dipakai >1 module, pindahkan ke `common/` atau buat `SharedModule`.

### Contoh `*.module.ts` — Product Module

```typescript
@Module({
  imports: [TypeOrmModule.forFeature([Product])],
  controllers: [ProductsController],
  providers: [ProductsService, ProductRepository],
  exports: [ProductsService],
})
export class ProductsModule {}
```

---

## Shared Module

Module yang menyediakan service/DTO/entity ke module lain.

```typescript
// shared/shared.module.ts
@Module({
  imports: [TypeOrmModule.forFeature([SharedEntity])],
  providers: [SharedService],
  exports: [SharedService],
})
export class SharedModule {}
```

Gunakan `SharedModule` untuk:
- Fitur yang digunakan banyak module tapi bukan cross-cutting (bukan `common/`).
- Service utilitas spesifik domain.

---

## Common / Core Module

Module untuk cross-cutting concerns yang **harus global**.

```typescript
// common/common.module.ts
@Module({
  providers: [
    { provide: APP_FILTER, useClass: HttpExceptionFilter },
    { provide: APP_GUARD, useClass: JwtAuthGuard },
    { provide: APP_INTERCEPTOR, useClass: TransformInterceptor },
  ],
  exports: [/* shared pipes, guards, etc */],
})
export class CommonModule {}
```

**Best practice**: Jangan jadikan `CommonModule` sebagai `@Global()` — lebih baik import eksplisit di `AppModule` agar dependency graph jelas.

---

## Naming Convention

| Elemen              | Convention            | Contoh                      |
|---------------------|-----------------------|-----------------------------|
| Module              | `kebab-case.module.ts`| `user-profile.module.ts`    |
| Controller          | `kebab-case.controller.ts` | `users.controller.ts`  |
| Service             | `kebab-case.service.ts`    | `auth.service.ts`       |
| Entity              | `kebab-case.entity.ts`     | `order-item.entity.ts`  |
| DTO                 | `kebab-case.dto.ts`        | `create-user.dto.ts`    |
| Guard               | `kebab-case.guard.ts`      | `jwt-auth.guard.ts`     |
| Interceptor         | `kebab-case.interceptor.ts`| `logging.interceptor.ts`|
| Filter              | `kebab-case.filter.ts`     | `http-exception.filter.ts`|
| Pipe                | `kebab-case.pipe.ts`       | `validation.pipe.ts`    |
| Decorator           | `kebab-case.decorator.ts`  | `current-user.decorator.ts`|
| Interface           | `kebab-case.interface.ts`  | `user.interface.ts`     |
| Test                | `*.spec.ts`                | `users.service.spec.ts` |
| E2E Test            | `*.e2e-spec.ts`            | `auth.e2e-spec.ts`      |

---

## Best Practices

### 1. Jangan buat module terlalu besar

Jika satu file service >300 baris, pecah menjadi beberapa service dalam satu module.

### 2. Barrel exports

Gunakan `index.ts` di setiap folder module untuk export rapi:

```typescript
// modules/users/index.ts
export * from './users.module';
export * from './users.service';
export * from './entities/user.entity';
```

### 3. Dependency Injection Direction

```
Controller → Service → Repository / ORM
```

- Controller hanya handle request/response.
- Service berisi business logic.
- Repository handle query database.

### 4. Circular module — hindari

Gunakan `forwardRef()` hanya jika benar-benar tak terhindarkan.

### 5. Environment validation

Validasi semua env variable di `config/env.validation.ts` dengan Joi/Zod agar app crash early saat startup jika config salah.

### 6. Test structure

```
__tests__/
├── unit/          # Pure unit test (mocked dependencies)
├── integration/   # Test dengan database nyata / test container
└── e2e/           # End-to-end (HTTP request penuh)
```

Atau cukup satu folder `__tests__/` dengan naming:

```
auth.controller.spec.ts    # unit test controller
auth.service.spec.ts       # unit test service
auth.integration.spec.ts   # integration test
auth.e2e-spec.ts           # e2e test (di test/ folder root)
```

### 7. Gunakan standalone files, bukan barrel inline

```typescript
// ✅ Lebih mudah ditemukan dan di-refactor
// users/dto/create-user.dto.ts

// ❌ Hindari inline di controller/service
```

### 8. Folder naming

| Folder      | Case           |
|-------------|----------------|
| Feature dir | `kebab-case`   |
| Entities    | `kebab-case`   |
| DTOs        | `kebab-case`   |
| Tests       | `__tests__`    |

> NestJS CLI generate file dengan kebab-case secara default — konsisten dengan itu.

### 9. File terlalu banyak? Grouping opsional

Jika satu module punya banyak file dalam satu kategori (misal 10+ DTO), boleh tambah sub-folder:

```
orders/
├── dto/
│   ├── requests/
│   │   ├── create-order.dto.ts
│   │   └── update-order.dto.ts
│   └── responses/
│       ├── order-response.dto.ts
│       └── order-list-response.dto.ts
```

Jangan lakukan ini jika hanya 2-3 file — flat lebih simpel.

### 10. Database — siapkan migration sejak awal

```bash
# TypeORM
npm run migration:generate -- src/database/migrations/InitSchema
npm run migration:run

# Prisma
npx prisma migrate dev --name init
```

---

## Diagram Dependency

```
AppModule
├── ConfigModule (global)
├── CommonModule
│   ├── Guards
│   ├── Filters
│   ├── Interceptors
│   └── Pipes
├── AuthModule
│   └── UsersModule (import)
├── UsersModule
├── ProductsModule
│   └── DatabaseModule (TypeORM/Prisma)
└── OrdersModule
    ├── ProductsModule (import)
    └── UsersModule (import)
```

---

## Winston Logger — Setup & Best Practices

> [Winston](https://github.com/winstonjs/winston) adalah logging library untuk Node.js. Di NestJS, Winston digunakan sebagai pengganti default logger agar lebih fleksibel (transport, format, level).

### 1. Installasi

```bash
npm install --save winston nest-winston
```

- `winston` — core library
- `nest-winston` — NestJS module wrapper (agar terintegrasi dengan DI)

### 2. Setup Global Logger Module

Buat `src/common/logger/winston-logger.module.ts`:

```typescript
import { Module } from '@nestjs/common';
import { WinstonModule } from 'nest-winston';
import * as winston from 'winston';

@Module({
  imports: [
    WinstonModule.forRoot({
      level: process.env.LOG_LEVEL || 'info',
      format: winston.format.combine(
        winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
        winston.format.errors({ stack: true }),
        winston.format.json(),
      ),
      transports: [
        // Console — warna, human-readable
        new winston.transports.Console({
          format: process.env.NODE_ENV !== 'production'
            ? winston.format.combine(
                winston.format.colorize(),
                winston.format.printf(
                  ({ timestamp, level, message, context, ...meta }) =>
                    `${timestamp} [${context || 'App'}] ${level}: ${message} ${
                      Object.keys(meta).length ? JSON.stringify(meta) : ''
                    }`,
                ),
              )
            : winston.format.json(),
        }),
        // File — error terpisah
        new winston.transports.File({
          filename: 'logs/error.log',
          level: 'error',
          maxsize: 5 * 1024 * 1024, // 5MB
          maxFiles: 5,
        }),
        // File — semua log
        new winston.transports.File({
          filename: 'logs/combined.log',
          maxsize: 10 * 1024 * 1024,
          maxFiles: 3,
        }),
      ],
    }),
  ],
  exports: [WinstonModule],
})
export class WinstonLoggerModule {}
```

### 3. Register di AppModule

```typescript
// app.module.ts
import { WinstonLoggerModule } from './common/logger/winston-logger.module';

@Module({
  imports: [
    WinstonLoggerModule,
    // ...
  ],
})
export class AppModule {}
```

### 4. Override NestJS Default Logger

Di `main.ts`, override logger bawaan NestJS:

```typescript
// main.ts
import { WINSTON_MODULE_NEST_PROVIDER } from 'nest-winston';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    bufferLogs: true, // tangkap log selama bootstrap
  });

  // Override logger NestJS dengan Winston
  app.useLogger(app.get(WINSTON_MODULE_NEST_PROVIDER));

  await app.listen(3000);
}
bootstrap();
```

### 5. Struktur Folder Logger

```
src/common/
├── logger/
│   ├── winston-logger.module.ts
│   └── winston-logger.provider.ts  # (opsional, jika perlu custom provider)
└── common.module.ts
```

### 6. Penggunaan di Service

```typescript
import { Injectable, Inject } from '@nestjs/common';
import { WINSTON_MODULE_PROVIDER } from 'nest-winston';
import { Logger as WinstonLogger } from 'winston';

@Injectable()
export class UsersService {
  constructor(
    @Inject(WINSTON_MODULE_PROVIDER) private readonly logger: WinstonLogger,
  ) {}

  findAll() {
    this.logger.info('Fetching all users', { module: 'Users' });
    // ...
  }

  create(dto: CreateUserDto) {
    this.logger.debug('Creating user', { email: dto.email });
    // ...
  }
}
```

Atau jika sudah override default logger di `main.ts`, cukup pakai `Logger` dari NestJS biasa:

```typescript
import { Injectable, Logger } from '@nestjs/common';

@Injectable()
export class UsersService {
  private readonly logger = new Logger(UsersService.name);

  findAll() {
    this.logger.log('Fetching all users');
    // Semua log otomatis terkirim ke Winston transports
  }
}
```

### 7. Best Practices Winston

| Praktik | Keterangan |
|---------|------------|
| **Log level** | Gunakan level sesuai konteks: `error` (runtime crash), `warn` (deprecation, edge case), `info` (operation sukses), `debug` (dev only), `verbose` (trace) |
| **Structured JSON** | Di production, gunakan `winston.format.json()` agar bisa di-parsing oleh log aggregator (ELK, Datadog, CloudWatch) |
| **Jangan log PII** | Jangan log password, token, nomor kartu kredit. Gunakan sanitizer interceptor jika perlu |
| **Context** | Selalu sertakan konteks (class name, request ID) agar tracing mudah |
| **File rotation** | Gunakan `maxsize` + `maxFiles` agar log tidak memenuhi disk |
| **Error stack** | Sertakan `winston.format.errors({ stack: true })` agar error stack tercatat |
| **Environment** | Dev: human-readable console colorized. Prod: JSON + file |

### 8. Log Directory di `.gitignore`

```gitignore
# logs
logs/
*.log
```

---

## Scalar — API Documentation

> [Scalar](https://scalar.com) adalah API reference UI modern pengganti Swagger UI. Integrasi dengan NestJS via `@nestjs/swagger` sebagai OpenAPI generator + `@scalar/nestjs-api-reference` sebagai renderer.

### 1. Installasi

```bash
npm install --save @nestjs/swagger @scalar/nestjs-api-reference
```

### 2. Setup di `main.ts`

```typescript
import { NestFactory } from '@nestjs/core';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { apiReference } from '@scalar/nestjs-api-reference';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Konfigurasi OpenAPI
  const config = new DocumentBuilder()
    .setTitle('GGAI API')
    .setDescription('API Documentation for GGAI-GeekGarden')
    .setVersion('1.0')
    .addBearerAuth()
    .addTag('auth', 'Authentication endpoints')
    .addTag('users', 'User management')
    .addTag('products', 'Product catalog')
    .addTag('orders', 'Order management')
    .build();

  const document = SwaggerModule.createDocument(app, config);

  // Scalar UI — lebih modern dari Swagger UI
  app.use(
    '/api/docs',
    apiReference({
      spec: {
        content: document,
      },
      theme: 'purple',   // tema: 'purple', 'blue', 'green', 'alternate', 'mars' dll
    }),
  );

  // Opsional: tetap sediakan endpoint raw OpenAPI JSON
  app.use('/api/docs-json', (_req, res) => {
    res.json(document);
  });

  await app.listen(3000);
}
bootstrap();
```

### 3. Setup Route Terpisah (Custom Controller)

Atau buat controller khusus agar bisa inject module dependencies:

```typescript
// modules/api-docs/api-docs.controller.ts
import { Controller, Get, Res } from '@nestjs/common';
import { ApiExcludeController } from '@nestjs/swagger';
import { Response } from 'express';
import { ApiDocsService } from './api-docs.service';

@ApiExcludeController() // sembunyikan dari dokumentasi
@Controller('api/docs')
export class ApiDocsController {
  constructor(private readonly apiDocsService: ApiDocsService) {}

  @Get()
  getDocs(@Res() res: Response) {
    const html = this.apiDocsService.getScalarHtml();
    res.send(html);
  }
}
```

### 4. Environment-based Enable/Disable

Jangan expose dokumentasi API di production tanpa autentikasi:

```typescript
// main.ts
if (process.env.ENABLE_API_DOCS === 'true') {
  app.use(
    '/api/docs',
    apiReference({
      spec: { content: document },
      theme: 'purple',
    }),
  );
}
```

Atau proteksi dengan guard:

```typescript
app.use('/api/docs', (_req, res, next) => {
  // Basic auth atau session check
  if (/* unauthorized */) {
    return res.status(401).send('Unauthorized');
  }
  next();
}, apiReference({ spec: { content: document } }));
```

### 5. Decorator untuk Dokumentasi Endpoint

Gunakan decorator `@nestjs/swagger` seperti biasa — Scalar render dari OpenAPI spec yang sama:

```typescript
import { ApiTags, ApiOperation, ApiBearerAuth, ApiQuery } from '@nestjs/swagger';

@ApiTags('users')
@Controller('users')
export class UsersController {
  @Get()
  @ApiBearerAuth()
  @ApiOperation({ summary: 'Get all users', description: 'Returns paginated list of users' })
  @ApiQuery({ name: 'page', required: false, type: Number })
  @ApiQuery({ name: 'limit', required: false, type: Number })
  findAll(@Query() pagination: PaginationDto) {
    return this.usersService.findAll(pagination);
  }
}
```

### 6. Scalar Theme Options

```typescript
apiReference({
  spec: { content: document },
  theme: 'purple',     // 'none' | 'default' | 'alternate' | 'moon' | 'purple' | 'blue' | 'green' | 'mars' | 'solarized'
  darkMode: true,      // toggle dark mode
  hideDownloadButton: false,
  showSidebar: true,
  withDefaultFonts: true,
  layout: 'modern',    // 'modern' | 'classic'
})
```

### 7. Best Practices Scalar

| Praktik | Keterangan |
|---------|------------|
| **Group dengan Tags** | Gunakan `@ApiTags()` untuk mengelompokkan endpoint per domain |
| **Bearer Auth** | Panggil `.addBearerAuth()` di DocumentBuilder + `@ApiBearerAuth()` per endpoint |
| **DTO Schema** | DTO otomatis jadi schema OpenAPI — pastikan pakai `class-validator` decorator |
| **Production** | Jangan expose docs di production tanpa auth — bisa bocorkan struktur API |
| **Versioning** | Update `setVersion()` tiap rilis agar dokumentasi sinkron |
| **Exclude internal** | Gunakan `@ApiExcludeController()` / `@ApiExcludeEndpoint()` untuk endpoint internal |

### 8. Akses Dokumentasi

- Scalar UI: `http://localhost:3000/api/docs`
- Raw OpenAPI JSON: `http://localhost:3000/api/docs-json`

---

## Referensi

- [NestJS Official Documentation — Modules](https://docs.nestjs.com/modules)
- [NestJS Official Documentation — CLI](https://docs.nestjs.com/cli/overview)
- [Bulletproof Node.js — Project Architecture](https://github.com/santiq/bulletproof-nodejs)
- [Vendia — Modular NestJS Architecture](https://github.com/vendia/nestjs-modular-architecture)

---

> **Catatan**: Struktur ini bisa disesuaikan dengan kebutuhan project. Untuk project kecil, boleh flatten dengan mengurangi folder seperti `interfaces/` atau `repositories/`. Untuk monorepo dengan banyak aplikasi, pertimbangkan Nx + NestJS.
