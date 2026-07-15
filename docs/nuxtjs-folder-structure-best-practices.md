# Nuxt.js Folder Structure — Best Practices

> Panduan struktur folder untuk project Nuxt 3 yang scalable, maintainable, dan production-ready.

---

## Daftar Isi

- [Prinsip Dasar](#prinsip-dasar)
- [Nuxt 3 Directory Auto-Import](#nuxt-3-directory-auto-import)
- [Struktur Lengkap](#struktur-lengkap)
- [Penjelasan Per Directory](#penjelasan-per-directory)
- [Pages & Routing](#pages--routing)
- [Server Engine (Nitro)](#server-engine-nitro)
- [Feature-Based Structure](#feature-based-structure)
- [Naming Convention](#naming-convention)
- [State Management](#state-management)
- [Best Practices](#best-practices)
- [Nuxt 2 → Nuxt 3 Migration Notes](#nuxt-2--nuxt-3-migration-notes)
- [Referensi](#referensi)

---

## Prinsip Dasar

1. **Convention over configuration** — Nuxt sudah punja struktur default, ikuti.
2. **Auto-import** — `composables/`, `utils/`, `components/` auto-import tanpa manual.
3. **Hybrid rendering** — pilih `ssr: true`, `ssr: false`, atau `routeRules` per halaman.
4. **Nitro server** — backend API di `server/` tanpa perlu server terpisah.
5. **Modular** — ekstrak fitur ke module atau `composables/` agar reusable.

---

## Nuxt 3 Directory Auto-Import

| Directory          | Auto-Import        | Contoh Penggunaan               |
|--------------------|--------------------|---------------------------------|
| `components/`      | ✅ Nested paths    | `<UiButton />`, `<AuthLoginForm />` |
| `composables/`     | ✅ Top-level       | `useAuth()`, `useCounter()`     |
| `utils/`           | ✅ Top-level       | `formatDate()`, `cn()`          |
| `middleware/`      | ✅ route middleware| `auth.global.ts`                |
| `plugins/`         | ❌ (manual regist) | Plugin Vue/Runtime               |
| `server/`          | ❌ H3 event handler| `defineEventHandler()`           |

---

## Struktur Lengkap

```
project-root/
├── .nuxt/                       # Generated — jangan di-commit
├── .output/                     # Build output — jangan di-commit
│
├── app/                         # App-layer config (Nuxt 3.15+)
│   ├── app.vue                  # Root component (entry point)
│   ├── router.options.ts        # Router customization
│   └── spa-loading-template.html
│
├── assets/                      # Uncompiled assets
│   ├── css/
│   │   ├── main.css
│   │   └── variables.css
│   ├── fonts/
│   │   └── inter.woff2
│   └── images/
│       ├── logo.svg
│       └── hero.png
│
├── components/                  # Vue components (auto-import)
│   ├── ui/                      # Primitives
│   │   ├── UiButton.vue
│   │   ├── UiInput.vue
│   │   ├── UiCard.vue
│   │   ├── UiModal.vue
│   │   └── UiTable.vue
│   ├── layout/                  # Layout components
│   │   ├── Navbar.vue
│   │   ├── Sidebar.vue
│   │   └── Footer.vue
│   └── shared/                  # Domain-agnostic shared
│       ├── ProductCard.vue
│       └── EmptyState.vue
│
├── composables/                 # Auto-imported composables (hooks)
│   ├── use-auth.ts
│   ├── use-debounce.ts
│   ├── use-media-query.ts
│   └── use-pagination.ts
│
├── layouts/                     # Layout templates
│   ├── default.vue              # Layout bawaan
│   ├── auth.vue                 # Layout halaman login/register
│   └── dashboard.vue            # Layout dashboard
│
├── middleware/                  # Route middleware (auto-import)
│   ├── auth.global.ts           # Global — jalan di semua route
│   └── guest.ts                 # Named — redirect if authenticated
│
├── pages/                       # File-based routing
│   ├── index.vue                # /
│   ├── login.vue                # /login
│   ├── register.vue             # /register
│   ├── dashboard.vue            # /dashboard
│   ├── products/
│   │   ├── index.vue            # /products
│   │   ├── [id].vue             # /products/:id
│   │   └── [id]/
│   │       └── edit.vue         # /products/:id/edit
│   ├── orders/
│   │   └── index.vue            # /orders
│   └── ---.vue                  # 404 catch-all
│
├── plugins/                     # Vue plugins (manual regist)
│   ├── pinia.ts                 # Pinia setup
│   ├── vee-validate.ts          # Form validation plugin
│   ├── toast.client.ts          # Client-only plugin
│   └── api.ts                   # Axios/fetch instance
│
├── public/                      # Static files (served as-is)
│   ├── favicon.ico
│   └── robots.txt
│
├── server/                      # Nitro backend API
│   ├── api/
│   │   ├── auth/
│   │   │   ├── login.post.ts
│   │   │   ├── register.post.ts
│   │   │   └── me.get.ts
│   │   ├── users/
│   │   │   ├── index.get.ts
│   │   │   └── [id].delete.ts
│   │   ├── products/
│   │   │   └── index.get.ts
│   │   └── health.get.ts
│   ├── middleware/
│   │   └── auth.ts              # Nitro middleware (semua request API)
│   ├── routes/                  # Custom routes (override)
│   ├── utils/
│   │   ├── prisma.ts            # DB client singleton
│   │   └── session.ts           # Session helper
│   ├── models/                  # TypeScript types untuk server
│   │   └── user.ts
│   └── tsconfig.json            # Server-specific TS config
│
├── utils/                       # Utility functions (auto-import)
│   ├── format-date.ts
│   ├── cn.ts                    # tailwind-merge + clsx
│   ├── constants.ts
│   └── validators.ts
│
├── shared/                      # Shared types/constants antara client & server (opsional)
│   └── types/
│       ├── api.ts               # ApiResponse<T>, PaginationMeta
│       └── user.ts
│
├── app.vue                      # Root component (alternate lokasi)
├── app.config.ts                # Runtime public config
├── nuxt.config.ts               # Nuxt configuration
├── tailwind.config.ts
├── tsconfig.json
├── .env
├── .env.example
├── .eslintrc.cjs
├── .prettierrc
├── package.json
├── Dockerfile
└── README.md
```

---

## Penjelasan Per Directory

### `app/` — Application Entry (Nuxt 3.15+)

Root component dan konfigurasi router.

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

> **Catatan**: `app.vue` bisa di root project atau di `app/app.vue`. Pilih satu, konsisten.

### `assets/` — Uncompiled Assets

File yang perlu diolah oleh bundler (Vite):

- `css/` — Global CSS, Tailwind imports, CSS variables
- `fonts/` — Font files (self-hosted)
- `images/` — Gambar yang di-import di komponen (bukan static URL)

Akses via `~assets/` alias atau import langsung:

```vue
<script setup>
import logo from '~/assets/images/logo.svg';
</script>
```

### `components/` — Vue Components (Auto-Import)

Struktur folder jadi nama komponen:

```
components/
├── ui/
│   ├── UiButton.vue      → <UiButton />
│   └── UiInput.vue       → <UiInput />
├── layout/
│   ├── Navbar.vue        → <LayoutNavbar />  (atau <Navbar /> jika unik)
│   └── Sidebar.vue       → <LayoutSidebar />
└── shared/
    └── ProductCard.vue   → <SharedProductCard />
```

**Prefix namespace** untuk menghindari konflik:

| Prefix    | Contoh File              | Komponen               |
|-----------|--------------------------|------------------------|
| `Ui`      | `ui/UiButton.vue`        | `<UiButton />`         |
| `Layout`  | `layout/LayoutNavbar.vue`| `<LayoutNavbar />`     |
| `Auth`    | `auth/AuthLoginForm.vue` | `<AuthLoginForm />`    |
| `Shared`  | `shared/ProductCard.vue` | `<SharedProductCard />`|

### `composables/` — Auto-Imported Composables

Setiap file `.ts` di sini auto-import ke semua komponen.

```typescript
// composables/use-auth.ts
export const useAuth = () => {
  const user = useState<User | null>('auth:user', () => null);

  const login = async (email: string, password: string) => {
    const data = await $fetch('/api/auth/login', { method: 'POST', body: { email, password } });
    user.value = data.user;
  };

  const logout = async () => {
    await $fetch('/api/auth/logout', { method: 'POST' });
    user.value = null;
  };

  return { user, login, logout };
};
```

Gunakan di komponen tanpa import:

```vue
<script setup>
const { user, login } = useAuth();
</script>
```

### `layouts/` — Layout Templates

Layout otomatis dipilih berdasarkan:

1. **`definePageMeta({ layout: 'auth' })`** — pilih layout manual
2. **File naming** — `auth.vue` → layout bernama `auth`
3. **`default.vue`** — layout default jika tidak specified

```vue
<!-- layouts/dashboard.vue -->
<template>
  <div class="flex h-screen">
    <LayoutSidebar />
    <main class="flex-1 overflow-y-auto p-6">
      <slot />
    </main>
  </div>
</template>
```

```vue
<!-- pages/dashboard.vue -->
<script setup lang="ts">
definePageMeta({
  layout: 'dashboard',
  middleware: 'auth',
});
</script>
```

### `middleware/` — Route Middleware

Global vs named:

```typescript
// middleware/auth.global.ts — jalan di semua route
export default defineNuxtRouteMiddleware((to, from) => {
  const { user } = useAuth();
  if (!user.value && to.path !== '/login') {
    return navigateTo('/login');
  }
});
```

```typescript
// middleware/guest.ts — dipasang manual
export default defineNuxtRouteMiddleware(() => {
  const { user } = useAuth();
  if (user.value) return navigateTo('/dashboard');
});
```

### `pages/` — File-Based Routing

Struktur folder = URL path:

```
pages/
├── index.vue                    → /
├── login.vue                    → /login
├── products/
│   ├── index.vue                → /products
│   ├── [id].vue                 → /products/:id
│   └── [id]/
│       └── edit.vue             → /products/:id/edit
├── orders/
│   └── index.vue                → /orders
└── ---.vue                      → /* (404 catch-all)
```

#### `definePageMeta`

```vue
<script setup lang="ts">
definePageMeta({
  layout: 'dashboard',
  middleware: ['auth'],
  title: 'Products',
  pageTransition: { name: 'page', mode: 'out-in' },
});
</script>
```

### `plugins/` — Vue Plugins

Hanya untuk plugin Vue / library yang butuh registrasi manual:

```typescript
// plugins/pinia.ts
import { createPinia } from 'pinia';

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.use(createPinia());
});
```

**Tip**: Plugin dengan suffix `.client.ts` hanya jalan di client, `.server.ts` hanya di server.

### `server/` — Nitro Backend

Backend API engine built-in. Setiap file di `server/api/` jadi endpoint otomatis.

```typescript
// server/api/products/index.get.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export default defineEventHandler(async (event) => {
  const query = getQuery(event);
  const page = Number(query.page) || 1;
  const limit = Number(query.limit) || 10;

  const products = await prisma.product.findMany({
    skip: (page - 1) * limit,
    take: limit,
  });

  return { data: products, meta: { page, limit } };
});
```

Nitro middleware untuk validasi auth:

```typescript
// server/middleware/auth.ts
export default defineEventHandler(async (event) => {
  // Skip auth untuk public endpoints
  if (event.path.startsWith('/api/auth') || event.path === '/api/health') return;

  const token = getHeader(event, 'authorization')?.replace('Bearer ', '');
  if (!token) throw createError({ statusCode: 401, message: 'Unauthorized' });

  event.context.user = await verifyToken(token);
});
```

### `utils/` — Utility Functions (Auto-Import)

```typescript
// utils/cn.ts
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

```typescript
// utils/format-date.ts
export function formatDate(date: Date | string, locale = 'id-ID') {
  return new Intl.DateTimeFormat(locale, {
    dateStyle: 'long',
    timeStyle: 'short',
  }).format(new Date(date));
}
```

### `shared/` — Client-Server Shared Types

Untuk tipe yang dipakai di client (`composables/`) dan server (`server/`):

```typescript
// shared/types/api.ts
export interface ApiResponse<T> {
  data: T;
  meta?: {
    page: number;
    limit: number;
    total: number;
  };
}

export interface ApiError {
  statusCode: number;
  message: string;
  details?: Record<string, string[]>;
}
```

Tambahkan alias di `nuxt.config.ts`:

```typescript
export default defineNuxtConfig({
  alias: {
    '@shared': './shared',
  },
});
```

---

## Pages & Routing

### Route Groups & Parent-Child

```
pages/
├── products/
│   ├── index.vue              → /products
│   └── [id].vue               → /products/:id
└── products.vue               → Parent layout untuk /products/*
```

Parent file (`products.vue`) wrapping child dengan `<NuxtPage />`:

```vue
<!-- pages/products.vue -->
<template>
  <div>
    <h1>Products Section</h1>
    <NuxtPage />  ← /products atau /products/:id dirender di sini
  </div>
</template>
```

### Navigation

```vue
<template>
  <nav>
    <NuxtLink to="/products">Products</NuxtLink>
    <NuxtLink :to="{ name: 'products-id', params: { id: '123' } }">
      Product Detail
    </NuxtLink>
  </nav>
</template>
```

### Route Rules (Hybrid Rendering)

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },                          // Static
    '/products': { swr: 3600 },                        // ISR every 1h
    '/dashboard/**': { ssr: false },                   // SPA
    '/api/**': { cors: true },                         // CORS for API
    '/admin/**': { redirect: '/login' },               // Redirect
  },
});
```

---

## Server Engine (Nitro)

Nitro adalah server engine bawaan Nuxt 3.

### Server Directory Structure

```
server/
├── api/                        # API endpoints (auto-routed)
│   ├── auth/
│   │   ├── login.post.ts
│   │   ├── register.post.ts
│   │   └── me.get.ts
│   ├── products/
│   │   ├── index.get.ts
│   │   └── [id].patch.ts
│   └── health.get.ts
├── middleware/                 # Nitro middleware (global untuk semua API)
│   └── auth.ts
├── routes/                     # Custom routes override
├── utils/                      # Server-only utilities
│   ├── prisma.ts
│   └── jwt.ts
└── models/                     # Server types
    └── user.ts
```

### File Naming = HTTP Method

| File                    | Method | Route               |
|-------------------------|--------|---------------------|
| `login.post.ts`         | POST   | `/api/auth/login`   |
| `me.get.ts`             | GET    | `/api/auth/me`      |
| `[id].patch.ts`         | PATCH  | `/api/products/:id` |
| `index.get.ts`          | GET    | `/api/products`     |

### Server Utils (Auto-Import Juga)

```typescript
// server/utils/prisma.ts
import { PrismaClient } from '@prisma/client';

export const prisma = new PrismaClient();
```

File di `server/utils/` auto-import di semua file server, tidak perlu import manual.

### Database Connection Best Practice

```typescript
// server/utils/db.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

---

## Feature-Based Structure

Untuk project besar, tambah layer feature di luar konvensi Nuxt:

```
project-root/
├── features/
│   ├── auth/
│   │   ├── components/        # Auth-specific components
│   │   │   ├── LoginForm.vue
│   │   │   └── RegisterForm.vue
│   │   ├── composables/       # Auth-specific composables
│   │   │   └── use-auth.ts
│   │   ├── server/            # Auth API handlers
│   │   │   ├── login.post.ts
│   │   │   └── register.post.ts
│   │   └── types.ts
│   │
│   └── products/
│       ├── components/
│       │   ├── ProductCard.vue
│       │   └── ProductList.vue
│       ├── composables/
│       │   └── use-products.ts
│       ├── server/
│       │   └── index.get.ts
│       └── types.ts
│
├── components/                 # Global shared components
├── pages/                      # Routing (import dari features)
└── server/api/                 # Global API (re-export dari features)
```

> **Catatan**: Feature folder tidak auto-import. Tambahkan alias di `nuxt.config.ts`.

---

## Naming Convention

| Elemen                    | Convention           | Contoh                          |
|---------------------------|----------------------|---------------------------------|
| Page file                 | `kebab-case.vue`     | `user-profile.vue`              |
| Component file            | `PascalCase.vue`     | `UiButton.vue`                  |
| Layout file               | `kebab-case.vue`     | `dashboard.vue`                 |
| Middleware file           | `kebab-case.ts`      | `auth.global.ts`                |
| Composable file           | `kebab-case.ts`      | `use-auth.ts`                   |
| Utility file              | `kebab-case.ts`      | `format-date.ts`                |
| Plugin file               | `kebab-case.ts`      | `pinia.ts`                      |
| Server API file           | `kebab-case.method.ts`| `login.post.ts`                |
| Type file                 | `kebab-case.ts`      | `api-types.ts`                  |
| Test file                 | `*.spec.ts`          | `login-form.spec.ts`            |
| Directory (non-routing)   | `kebab-case`         | `user-profile/`                 |

---

## State Management

### useState (Built-in)

```typescript
// composables/use-counter.ts
export const useCounter = () => {
  return useState<number>('counter', () => 0);
};
```

### Pinia (Recommended untuk complex state)

```bash
npm install @pinia/nuxt
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
});
```

```typescript
// stores/cart.ts
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([]);
  const total = computed(() => items.value.reduce((sum, i) => sum + i.price * i.qty, 0));

  function add(product: Product) { items.value.push({ ...product, qty: 1 }); }
  function remove(id: string) { items.value = items.value.filter(i => i.id !== id); }

  return { items, total, add, remove };
});
```

### State Management Decision

| Kebutuhan             | Tools                           |
|-----------------------|---------------------------------|
| Local component state | `ref()`, `reactive()`           |
| Shared client state   | `useState()`                    |
| Complex global state  | Pinia                           |
| Server state (cache)  | `useFetch()` / `useAsyncData()` |
| URL state             | `useRoute()`, `useRouter()`     |

---

## Best Practices

### 1. Gunakan `useFetch`, hindari `$fetch` langsung di setup

```vue
<script setup>
// ✅ Auto-dedupe, cache, error handling
const { data: products, pending, error } = await useFetch('/api/products', {
  query: { page: 1, limit: 10 },
});

// ❌ Tidak auto-handle loading/error
const products = await $fetch('/api/products');
</script>
```

### 2. Prefix komponen untuk menghindari konflik

```
components/
├── ui/UiButton.vue          → <UiButton />
├── auth/AuthLoginForm.vue   → <AuthLoginForm />
├── products/
│   ├── ProductCard.vue      → <ProductsProductCard />
│   └── ProductList.vue      → <ProductsProductList />
```

Nuxt auto-resolve nested path jadi PascalCase prefix.

### 3. Colocation — test dekat komponen

```
components/
├── ui/
│   ├── UiButton.vue
│   └── __tests__/
│       └── UiButton.spec.ts     # ✅ Colocated
├── auth/
│   ├── AuthLoginForm.vue
│   └── AuthLoginForm.spec.ts    # ✅ Colocated
```

### 4. Environment variables

```bash
# .env
NUXT_PUBLIC_API_URL=http://localhost:3000
NUXT_PUBLIC_SITE_URL=https://ggai.app
DATABASE_URL=postgresql://...
AUTH_SECRET=...
```

Akses di client:

```typescript
const apiUrl = useRuntimeConfig().public.apiUrl;
const siteUrl = useRuntimeConfig().public.siteUrl;
```

Akses di server:

```typescript
const dbUrl = useRuntimeConfig().databaseUrl;
const authSecret = useRuntimeConfig().authSecret;
```

Config di `nuxt.config.ts`:

```typescript
export default defineNuxtConfig({
  runtimeConfig: {
    databaseUrl: '',     // server-only (dari .env)
    authSecret: '',
    public: {
      apiUrl: '',        // exposed to client
      siteUrl: '',
    },
  },
});
```

### 5. Typesafe server routes

```typescript
// server/api/products/index.get.ts
export default defineEventHandler(async (event) => {
  const products = await prisma.product.findMany();
  return products satisfies Product[];
});
```

Gunakan `zod` untuk validasi body/query:

```typescript
// server/api/products/index.post.ts
import { z } from 'zod';

const bodySchema = z.object({
  name: z.string().min(1),
  price: z.number().positive(),
});

export default defineEventHandler(async (event) => {
  const body = await readBody(event);
  const parsed = bodySchema.parse(body); // auto-throw 400 jika invalid
  const product = await prisma.product.create({ data: parsed });
  return product;
});
```

### 6. Gunakan `server/utils/` untuk shared server logic

```typescript
// server/utils/jwt.ts
import jwt from 'jsonwebtoken';

export function generateToken(payload: object) {
  return jwt.sign(payload, useRuntimeConfig().authSecret, { expiresIn: '7d' });
}

export function verifyToken(token: string) {
  return jwt.verify(token, useRuntimeConfig().authSecret);
}
```

Auto-import di semua server file — tanpa `import`.

### 7. Gunakan `app.config.ts` untuk public config

```typescript
// app.config.ts
export default defineAppConfig({
  title: 'GGAI App',
  theme: {
    primary: '#6366f1',
    gray: '#6b7280',
  },
  features: {
    enableChat: true,
    enableReviews: false,
  },
});
```

Akses di komponen:

```typescript
const appConfig = useAppConfig();
```

### 8. Lazy load komponen berat

```vue
<template>
  <!-- Hanya load saat visible / dibutuhkan -->
  <LazyDashboardChart />
  <LazyProductGallery />
</template>
```

Prefix `Lazy` membuat Nuxt code-split komponen.

### 9. Hydration mismatch — handle dengan ClientOnly

```vue
<template>
  <ClientOnly>
    <DarkModeToggle />
    <template #fallback>
      <div class="h-8 w-8 bg-gray-200 rounded animate-pulse" />
    </template>
  </ClientOnly>
</template>
```

### 10. Module system — jangan taro semua di root

```typescript
// nuxt.config.ts — pisahkan module besar ke file terpisah
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt',
    '@vueuse/nuxt',
    '~/modules/auth',         // custom module
  ],
});
```

### 11. Gunakan `error.vue` untuk global error handling

```vue
<!-- error.vue (di root, bukan di app/) -->
<script setup lang="ts">
const props = defineProps({
  error: Object,
});
</script>

<template>
  <div>
    <h1>{{ error?.statusCode }}</h1>
    <p>{{ error?.message }}</p>
    <button @click="clearError({ redirect: '/' })">Go Home</button>
  </div>
</template>
```

### 12. Satu komponen per file

```vue
<!-- ✅ Satu komponen per file -->
<!-- components/ui/UiButton.vue -->

<!-- ❌ Jangan gabung multiple components dalam satu file -->
```

Exception: komponen kecil yang tightly coupled (label + input dalam satu file).

---

## Nuxt 2 → Nuxt 3 Migration Notes

| Nuxt 2                   | Nuxt 3                          |
|--------------------------|----------------------------------|
| `nuxt.config.js`         | `nuxt.config.ts`                 |
| `@nuxtjs/axios`          | `useFetch()` / `$fetch`          |
| `@nuxtjs/auth-next`      | Custom `useAuth()` composable     |
| `store/` (Vuex)          | Pinia `stores/`                   |
| `static/`                | `public/`                         |
| `plugins/` context inject| `provide()` via `defineNuxtPlugin`|
| `asyncData()`            | `useAsyncData()` / `useFetch()`   |
| `fetch()` hook           | `useAsyncData()`                  |
| No auto-import           | Auto-import components/composables/utils |
| Express server           | Nitro (H3)                        |
| `serverMiddleware/`      | `server/middleware/`               |

---

## Referensi

- [Nuxt 3 Docs — Directory Structure](https://nuxt.com/docs/guide/directory-structure)
- [Nuxt 3 Docs — Auto-imports](https://nuxt.com/docs/guide/concepts/auto-imports)
- [Nuxt 3 Docs — Server Engine (Nitro)](https://nuxt.com/docs/guide/concepts/server-engine)
- [Nitro Documentation](https://nitro.unjs.io/)
- [Pinia Documentation](https://pinia.vuejs.org/)
- [Nuxt 3 Examples](https://nuxt.com/docs/examples)

---

> **Catatan**: Struktur ini optimal untuk Nuxt 3 (v3+). Nuxt 2 memiliki struktur berbeda (`pages/` sama, tapi tanpa `server/`, `composables/`, `utils/` auto-import). Sesuaikan dengan versi Nuxt yang dipakai.
