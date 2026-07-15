# Next.js Folder Structure — Best Practices

> Panduan struktur folder untuk project Next.js (App Router) yang scalable, maintainable, dan production-ready.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Struktur Lengkap](#struktur-lengkap)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [App Router Structure](#app-router-structure)
- [Feature Sliced Design](#feature-sliced-design)
- [Naming Convention](#naming-convention)
- [State Management](#state-management)
- [API Routes (Route Handlers)](#api-routes-route-handlers)
- [Best Practices](#best-practices)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **App Router first** — semua route pakai `app/`, hindari `pages/`.
2. **Colocation** — taruh file yang berkaitan dekat dengan penggunaannya.
3. **Feature-based** — kumpulkan komponen, hooks, utils per fitur, bukan per tipe.
4. **Server Component by default** — minimalisir `'use client'`.
5. **Scalable** — mudah navigasi meski 100+ file.

---

## Struktur Lengkap

```
project-root/
├── public/
│   ├── images/
│   ├── icons/
│   ├── fonts/
│   └── favicon.ico
│
├── src/
│   ├── app/                      # App Router — routing & layout
│   │   ├── (auth)/               # Route group — login/register
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx
│   │   │
│   │   ├── (dashboard)/          # Route group — authenticated pages
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx
│   │   │   ├── products/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── [id]/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── edit/
│   │   │   │   │       └── page.tsx
│   │   │   │   └── new/
│   │   │   │       └── page.tsx
│   │   │   └── layout.tsx
│   │   │
│   │   ├── api/                   # Route Handlers (backend)
│   │   │   ├── auth/
│   │   │   │   ├── login/
│   │   │   │   │   └── route.ts
│   │   │   │   └── register/
│   │   │   │       └── route.ts
│   │   │   ├── users/
│   │   │   │   └── route.ts
│   │   │   └── webhooks/
│   │   │       └── stripe/
│   │   │           └── route.ts
│   │   │
│   │   ├── layout.tsx            # Root layout
│   │   ├── page.tsx              # Landing page
│   │   ├── loading.tsx           # Global loading state
│   │   ├── error.tsx             # Global error boundary
│   │   ├── not-found.tsx         # 404 page
│   │   ├── sitemap.ts            # Dynamic sitemap
│   │   └── robots.ts             # Robots config
│   │
│   ├── components/               # Shared UI components
│   │   ├── ui/                   # Primitives (shadcn/ui style)
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── card.tsx
│   │   │   ├── modal.tsx
│   │   │   ├── table.tsx
│   │   │   └── index.ts          # Barrel export
│   │   ├── layout/               # Layout components
│   │   │   ├── navbar.tsx
│   │   │   ├── sidebar.tsx
│   │   │   ├── footer.tsx
│   │   │   └── header.tsx
│   │   └── shared/               # Domain-agnostic shared components
│   │       ├── pagination.tsx
│   │       └── empty-state.tsx
│   │
│   ├── features/                 # Feature modules
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   │   ├── login-form.tsx
│   │   │   │   ├── register-form.tsx
│   │   │   │   └── oauth-buttons.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── use-login.ts
│   │   │   │   └── use-session.ts
│   │   │   ├── actions.ts        # Server Actions
│   │   │   ├── api.ts            # API client functions
│   │   │   ├── schemas.ts        # Zod validation schemas
│   │   │   └── types.ts
│   │   │
│   │   ├── products/
│   │   │   ├── components/
│   │   │   │   ├── product-card.tsx
│   │   │   │   ├── product-list.tsx
│   │   │   │   └── product-form.tsx
│   │   │   ├── hooks/
│   │   │   │   ├── use-products.ts
│   │   │   │   └── use-product.ts
│   │   │   ├── actions.ts
│   │   │   ├── api.ts
│   │   │   ├── schemas.ts
│   │   │   └── types.ts
│   │   │
│   │   └── orders/
│   │       ├── components/
│   │       │   ├── order-card.tsx
│   │       │   └── order-status-badge.tsx
│   │       ├── hooks/
│   │       │   └── use-orders.ts
│   │       ├── actions.ts
│   │       ├── api.ts
│   │       ├── schemas.ts
│   │       └── types.ts
│   │
│   ├── lib/                      # Utility library
│   │   ├── db.ts                 # Database client (Prisma/Drizzle)
│   │   ├── auth.ts               # Auth config (NextAuth/Auth.js)
│   │   ├── api-client.ts         # Axios/fetch wrapper
│   │   ├── utils.ts              # Utility functions (cn, format, dll)
│   │   ├── constants.ts          # App constants
│   │   ├── logger.ts             # Logger instance
│   │   └── email.ts              # Email service
│   │
│   ├── hooks/                    # Global shared hooks
│   │   ├── use-debounce.ts
│   │   ├── use-media-query.ts
│   │   ├── use-local-storage.ts
│   │   └── use-intersection-observer.ts
│   │
│   ├── providers/                # React context providers
│   │   ├── theme-provider.tsx
│   │   ├── auth-provider.tsx
│   │   ├── query-provider.tsx    # TanStack Query
│   │   └── index.tsx             # Composisi semua provider
│   │
│   ├── styles/                   # Global styles
│   │   ├── globals.css
│   │   └── variables.css
│   │
│   ├── types/                    # Global TypeScript types
│   │   ├── index.ts
│   │   ├── api.ts
│   │   └── next.ts               # Next.js extended types
│   │
│   └── middleware.ts             # Next.js middleware
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│       └── playwright/
│
├── .env.local
├── .env.example
├── .eslintrc.json
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

---

## Penjelasan Per Directory

### `src/app/` — Routing & Layouts

App Router adalah entry point semua halaman. Struktur folder = URL path.

| File             | Fungsi |
|------------------|--------|
| `layout.tsx`     | Layout wrapper — persist antar navigasi |
| `page.tsx`       | Halaman utama segmen route |
| `loading.tsx`    | Suspense fallback — tampilkan skeleton |
| `error.tsx`      | Error boundary — catch error di segmen |
| `not-found.tsx`  | 404 page spesifik segmen |
| `template.tsx`   | Layout tanpa persist (re-mount tiap navigasi) |
| `default.tsx`    | Fallback parallel route |
| `route.ts`       | Route Handler (API endpoint) |
| `sitemap.ts`     | Dynamic sitemap generation |
| `robots.ts`      | Robots.txt generation |

#### Route Groups `(folder)`

Gunakan `(group)/` untuk grouping tanpa mempengaruhi URL:

```
app/
├── (auth)/                    # /login, /register — tanpa prefix /auth
│   ├── login/page.tsx
│   └── register/page.tsx
├── (dashboard)/               # /products, /orders — tanpa prefix /dashboard
│   ├── products/page.tsx
│   └── orders/page.tsx
└── layout.tsx                 # Root layout
```

Berguna untuk:
- Layout berbeda per group.
- Organisasi routing yang bersih.
- Middleware targeting specific group.

### `src/components/` — Shared UI Components

Komponen **reusable** yang tidak terikat domain bisnis.

- `ui/` — Primitives (Button, Input, Card, Modal). Jika pakai shadcn/ui, ini tempatnya.
- `layout/` — Layout elements (Navbar, Sidebar, Footer).
- `shared/` — Domain-agnostic complex components (Pagination, EmptyState, DataTable).

### `src/features/` — Feature Modules (Domain)

**Heart of the project.** Setiap fitur bisnis punya folder sendiri.

```
features/auth/
├── components/       # React components spesifik fitur
│   ├── login-form.tsx
│   └── register-form.tsx
├── hooks/            # React hooks spesifik fitur
│   └── use-session.ts
├── actions.ts        # Server Actions
├── api.ts            # API fetch functions
├── schemas.ts        # Zod schemas untuk validasi
└── types.ts          # TypeScript types spesifik fitur
```

**Rule**: Jika komponen/hook hanya dipakai satu fitur → taruh di dalam fitur tsb. Jika dipakai lintas fitur → pindahkan ke `components/shared/` atau `hooks/`.

### `src/lib/` — Application Library

Pure functions, configuration, dan service clients.

| File          | Fungsi |
|---------------|--------|
| `db.ts`       | Database client (Prisma/Drizzle) — singleton |
| `auth.ts`     | NextAuth/Auth.js v5 config |
| `api-client.ts` | Wrapper `fetch` — base URL, interceptor, error handling |
| `utils.ts`    | Utility: `cn()` (tailwind-merge), formatDate, slugify |
| `constants.ts`| Enum & constants: ROLES, STATUS, PAGINATION_LIMIT |
| `logger.ts`   | Logger instance (pino/winston) |
| `email.ts`    | Email service (Resend/SendGrid) |

### `src/hooks/` — Global Custom Hooks

Hooks yang digunakan di banyak tempat:

- `use-debounce` — debounce input/value
- `use-media-query` — responsive breakpoint detection
- `use-local-storage` — persist state ke localStorage
- `use-intersection-observer` — infinite scroll / lazy load

Hooks spesifik fitur tetap di `features/*/hooks/`.

### `src/providers/` — React Context Providers

Provider wrapping root layout:

```typescript
// providers/index.tsx
'use client';

import { ThemeProvider } from './theme-provider';
import { AuthProvider } from './auth-provider';
import { QueryProvider } from './query-provider';

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <ThemeProvider>
      <AuthProvider>
        <QueryProvider>
          {children}
        </QueryProvider>
      </AuthProvider>
    </ThemeProvider>
  );
}
```

### `src/types/` — Global Types

TypeScript types yang digunakan lintas fitur:

- `api.ts` — generic `ApiResponse<T>`, `PaginationParams`, `PaginationMeta`
- `next.ts` — extended Next.js types (NextRequestWithUser, dll)

### `public/` — Static Assets

- `images/` — gambar (logo, hero, ilustrasi)
- `icons/` — SVG icons / favicon
- `fonts/` — font file self-hosted (opsional)

**Best practice**: Optimasi gambar dengan `next/image` — taruh di `public/` hanya jika perlu akses langsung via URL.

---

## App Router Structure

### Layout Hierarchy

```
app/layout.tsx              ← Root layout (html, body, providers)
├── app/(auth)/layout.tsx   ← Auth layout (centered form)
│   ├── app/(auth)/login/page.tsx
│   └── app/(auth)/register/page.tsx
└── app/(dashboard)/layout.tsx  ← Dashboard layout (sidebar + navbar)
    ├── app/(dashboard)/products/page.tsx
    └── app/(dashboard)/orders/page.tsx
```

### Server vs Client Component Boundary

```
app/(dashboard)/products/page.tsx    ← Server Component (fetch data)
└── <ProductList>                    ← Client Component (interaktif)
    └── <ProductCard>                ← Server Component (render data)
        └── <AddToCartButton>        ← Client Component (event handler)
```

**Golden rule**: Start sebagai Server Component. Pindahkan `'use client'` ke leaf terendah yang benar-benar butuh interaktivitas.

---

## Feature Sliced Design

Untuk project besar, adopsi [Feature-Sliced Design](https://feature-sliced.design/) layers:

```
src/
├── app/          # App Router — routing
├── processes/    # Business processes (multi-step: checkout flow)
├── pages/        # Composisi halaman dari entity + features
├── features/     # User interactions (auth, add-to-cart, search)
├── entities/     # Business entities (user, product, order)
├── shared/       # UI primitives, lib, config
│
# Di Next.js, cukup 3 layer utama:
├── app/          # Routing + pages
├── features/     # Feature modules
└── shared/       # Components + lib + types
```

---

## Naming Convention

| Elemen                    | Convention         | Contoh                          |
|---------------------------|--------------------|---------------------------------|
| Route directory           | `kebab-case`       | `/user-profile/settings`        |
| Page file                 | `page.tsx`         | `page.tsx`                      |
| Layout file               | `layout.tsx`       | `layout.tsx`                    |
| Loading file              | `loading.tsx`      | `loading.tsx`                   |
| Error file                | `error.tsx`        | `error.tsx`                     |
| Route Handler             | `route.ts`         | `route.ts`                      |
| Component file            | `kebab-case.tsx`   | `product-card.tsx`              |
| Hook file                 | `use-kebab-case.ts`| `use-debounce.ts`               |
| Server Action             | `actions.ts`       | `actions.ts` (per feature)      |
| API client                | `api.ts`           | `api.ts` (per feature)          |
| Validation schema         | `schemas.ts`       | `schemas.ts`                    |
| Type file                 | `types.ts`         | `types.ts`                      |
| Utility file              | `kebab-case.ts`    | `format-date.ts`                |
| Test file                 | `*.test.ts(x)`     | `login-form.test.tsx`           |
| E2E test file             | `*.spec.ts`        | `auth.spec.ts`                  |

---

## State Management

| Layer              | Tools                             |
|--------------------|-----------------------------------|
| Server State       | Server Component (fetch) / TanStack Query / SWR |
| Client State       | React Context / Zustand / Jotai   |
| URL State          | `useSearchParams` / `useRouter`   |
| Form State         | React Hook Form + Zod             |

### Recommended Stack

```
Server data fetching  → Server Components + TanStack Query (client cache)
Global client state   → Zustand (lightweight, no boilerplate)
Form validation       → React Hook Form + Zod
URL params            → useSearchParams + nuqs (type-safe query params)
```

### Struktur untuk Zustand Store

```
src/
├── stores/
│   ├── cart-store.ts
│   ├── ui-store.ts
│   └── auth-store.ts
```

---

## API Routes (Route Handlers)

Gunakan Route Handler (`app/api/*/route.ts`) untuk API endpoint.

### Struktur

```
app/api/
├── auth/
│   ├── login/route.ts
│   ├── register/route.ts
│   └── me/route.ts
├── users/
│   ├── route.ts          # GET /api/users, POST /api/users
│   └── [id]/
│       └── route.ts      # GET /api/users/:id, PATCH, DELETE
├── webhooks/
│   ├── stripe/route.ts
│   └── clerk/route.ts
└── health/
    └── route.ts
```

### Best Practice Route Handler

```typescript
// app/api/users/route.ts
import { NextResponse } from 'next/server';
import { db } from '@/lib/db';

export async function GET(req: Request) {
  const { searchParams } = new URL(req.url);
  const page = Number(searchParams.get('page')) || 1;
  const limit = Number(searchParams.get('limit')) || 10;

  const users = await db.user.findMany({
    skip: (page - 1) * limit,
    take: limit,
  });

  return NextResponse.json({
    data: users,
    meta: { page, limit },
  });
}

export async function POST(req: Request) {
  const body = await req.json();
  // Validasi dengan Zod
  const user = await db.user.create({ data: body });
  return NextResponse.json({ data: user }, { status: 201 });
}
```

---

## Best Practices

### 1. Gunakan `@` alias, hindari relative import

```typescript
// ✅
import { Button } from '@/components/ui/button';
import { prisma } from '@/lib/db';

// ❌
import { Button } from '../../../components/ui/button';
```

Config di `tsconfig.json`:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### 2. Colocation — taruh test dekat komponen

```
features/auth/
├── components/
│   ├── login-form.tsx
│   ├── login-form.test.tsx       # ✅ Colocated
│   └── login-form.stories.tsx    # ✅ Storybook colocated
├── hooks/
│   ├── use-login.ts
│   └── use-login.test.ts         # ✅ Colocated
```

Atau gunakan `__tests__/` di dalam fitur untuk integration test.

### 3. Barrel export untuk public API fitur

```typescript
// features/products/index.ts
export { ProductCard } from './components/product-card';
export { ProductList } from './components/product-list';
export { useProducts } from './hooks/use-products';
export { createProductSchema } from './schemas';
export type { Product } from './types';
```

**Tapi jangan barrel export komponen UI** — import langsung dari file spesifik agar tree-shaking optimal.

### 4. Satu concern per file

```typescript
// ✅ Satu komponen per file
// components/ui/button.tsx
// components/ui/input.tsx

// ❌ Jangan gabung beberapa komponen dalam satu file besar
```

Exception: komponen kecil yang saling terkait erat (misal `label.tsx` + `input.tsx` dalam satu file masih acceptable).

### 5. Batch import providers

```typescript
// providers/index.tsx — semua provider di satu file
export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <ThemeProvider>
      <AuthProvider>
        <QueryProvider>
          {children}
        </QueryProvider>
      </AuthProvider>
    </ThemeProvider>
  );
}
```

### 6. Environment variables

```bash
# .env.local — jangan commit!
DATABASE_URL="postgresql://..."
NEXT_PUBLIC_API_URL="http://localhost:3000"
AUTH_SECRET="..."
```

Validasi env runtime dengan Zod:

```typescript
// lib/env.ts
import { z } from 'zod';

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NEXT_PUBLIC_API_URL: z.string().url(),
  AUTH_SECRET: z.string().min(32),
});

export const env = envSchema.parse(process.env);
```

### 7. Gunakan `loading.tsx` + Suspense

```typescript
// app/(dashboard)/products/loading.tsx
export default function ProductsLoading() {
  return (
    <div className="grid gap-4 md:grid-cols-3">
      {Array.from({ length: 6 }).map((_, i) => (
        <Skeleton key={i} className="h-48 w-full" />
      ))}
    </div>
  );
}
```

### 8. Server Actions — satu file per fitur

```typescript
// features/products/actions.ts
'use server';

import { revalidatePath } from 'next/cache';
import { db } from '@/lib/db';
import { createProductSchema } from './schemas';

export async function createProduct(formData: FormData) {
  const data = createProductSchema.parse(Object.fromEntries(formData));
  await db.product.create({ data });
  revalidatePath('/products');
}

export async function deleteProduct(id: string) {
  await db.product.delete({ where: { id } });
  revalidatePath('/products');
}
```

### 9. Middleware — minimalisir logic

```typescript
// src/middleware.ts
import { auth } from '@/lib/auth';
import { NextResponse } from 'next/server';

export default auth((req) => {
  const isAuth = !!req.auth;
  const isAuthPage = req.nextUrl.pathname.startsWith('/login');

  if (isAuthPage) {
    if (isAuth) return NextResponse.redirect(new URL('/dashboard', req.url));
    return null;
  }

  if (!isAuth) {
    return NextResponse.redirect(new URL('/login', req.url));
  }

  return null;
});

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

### 10. Jangan simpan data besar di client component

Server Component fetch data → kirim props ke Client Component.

```typescript
// ✅ Server Component fetch data
app/products/page.tsx
  ↓ (props)
features/products/components/product-list.tsx ← Client Component (hanya render)

// ❌ Jangan fetch di Client Component kalau bisa di Server Component
```

### 11. Metadata

Gunakan `generateMetadata` untuk SEO:

```typescript
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Products - GGAI',
  description: 'Browse our product catalog',
};

// Atau dynamic
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const product = await getProduct(params.id);
  return { title: `${product.name} - GGAI` };
}
```

---

## Project Size Adaptation

| Ukuran Project        | Struktur                         |
|----------------------|----------------------------------|
| **Kecil** (<10 pages) | Flat: semua di `components/` + `lib/` |
| **Sedang** (10-30 pages) | `features/` per domain + shared components |
| **Besar** (30+ pages) | Feature Sliced — entities + features + processes + shared |

---

## Referensi

- [Next.js Project Structure](https://nextjs.org/docs/getting-started/project-structure)
- [Next.js App Router](https://nextjs.org/docs/app/building-your-application/routing)
- [Feature-Sliced Design](https://feature-sliced.design/)
- [shadcn/ui](https://ui.shadcn.com/)
- [Next.js Advanced Patterns (Leerob)](https://github.com/leerob/nextjs-patterns)

---

> **Catatan**: Struktur ini optimal untuk Next.js App Router (v13+). Untuk Pages Router, gunakan struktur berbeda dengan `pages/` sebagai routing layer. Sesuaikan dengan skala tim dan kompleksitas project.
