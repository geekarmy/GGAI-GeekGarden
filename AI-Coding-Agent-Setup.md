# Setup AI Coding Agent dengan Pi, GGAI, RTK, dan 9Router

Dokumentasi ini menjelaskan cara menghubungkan AI Coding Agent ke layanan GGAI menggunakan Pi Agent.

Terdapat beberapa metode koneksi:

1. **Pi langsung ke GGAI**
   Direkomendasikan untuk setup awal karena lebih sederhana.

2. **Pi melalui 9Router ke GGAI**
   Digunakan jika membutuhkan fitur tambahan dari 9Router seperti endpoint terpusat, pengelolaan provider, skills, plugins, dan routing model.

3. **Pi melalui 9Router ke beberapa provider**
   Digunakan jika pengguna sudah memiliki langganan atau akses ke provider AI lain seperti OpenCode, OpenAI, Anthropic, Google Gemini, OpenRouter, atau provider lain yang didukung.

4. **Pi langsung ke beberapa provider**
   Setiap provider dapat ditambahkan secara terpisah pada file `.pi/agent/models.json` tanpa menggunakan 9Router.

---

# Arsitektur Koneksi

## Metode 1 — Pi Langsung ke GGAI

```text
Developer
    │
    ▼
Pi Agent
    │
    ▼
GGAI API
    │
    ├── Ornith 9B
    └── DeepSeek V4 (Limited)
```

Metode ini direkomendasikan untuk pengguna baru karena memiliki konfigurasi paling sederhana.

---

## Metode 2 — Pi melalui 9Router

```text
Developer
    │
    ▼
Pi Agent
    │
    ▼
9Router
    │
    ▼
GGAI API
    │
    ├── Ornith 9B
    └── DeepSeek V4 (Limited)
```

Gunakan metode ini jika membutuhkan:

* Endpoint AI terpusat
* Pengelolaan provider
* Pengelolaan API key
* Skills tambahan
* Plugins tambahan
* Routing model

---

## Metode 3 — Pi melalui 9Router dengan Beberapa Provider

```text
                     ┌── GGAI
                     │    ├── Ornith 9B
                     │    └── DeepSeek V4
                     │
Developer ──► Pi ──► 9Router
                     │
                     ├── OpenCode
                     ├── OpenAI
                     ├── Anthropic
                     ├── Google Gemini
                     ├── OpenRouter
                     └── Provider Lain
```

Jika pengguna sudah memiliki langganan atau akses ke provider AI lain, provider tersebut dapat ditambahkan ke 9Router masing-masing.

Dengan metode ini, model kantor dan model pribadi dapat digunakan melalui satu endpoint 9Router.

---

# 1. Persiapan Endpoint dan API Key

Sebelum memulai instalasi, minta informasi berikut kepada administrator:

* Endpoint GGAI
* API key GGAI

Endpoint GGAI:

```text
https://ggai-developer.geekdevops.my.id/v1
```

API key akan diberikan oleh administrator.

Contoh:

```text
sk-xxxxxxxxxxxxxxxx
```

> Jangan membagikan API key melalui repository GitHub, screenshot, dokumentasi publik, source code, GitHub Issue, atau group chat yang tidak memiliki akses.

Pastikan kebutuhan berikut sudah tersedia:

* Git
* Node.js versi LTS
* npm
* Terminal
* IDE atau code editor
* Project yang akan dikerjakan

IDE yang dapat digunakan:

* Visual Studio Code
* Cursor
* Kiro
* Zed
* IDE lain yang memiliki terminal

---

# 2. Instalasi Node.js

Pi Agent membutuhkan Node.js.

Periksa apakah Node.js sudah tersedia:

```bash
node --version
```

Periksa npm:

```bash
npm --version
```

Jika versi Node.js dan npm muncul, lanjutkan ke instalasi Pi Agent.

Disarankan menggunakan Node.js versi LTS.

## Instalasi Node.js Menggunakan NVM

NVM memudahkan instalasi dan pergantian versi Node.js.

### Linux dan macOS

Install NVM mengikuti dokumentasi resmi NVM.

Setelah NVM tersedia, install Node.js versi LTS:

```bash
nvm install --lts
```

Gunakan Node.js versi LTS:

```bash
nvm use --lts
```

Jadikan versi LTS sebagai default:

```bash
nvm alias default 'lts/*'
```

Verifikasi:

```bash
node --version
npm --version
```

### Windows

Pada Windows, gunakan NVM for Windows atau installer Node.js versi LTS.

Setelah instalasi selesai, tutup dan buka kembali PowerShell atau Windows Terminal.

Verifikasi:

```powershell
node --version
npm --version
```

---

# 3. Instalasi Pi Agent

Install Pi Agent dengan mengikuti dokumentasi resmi:

https://pi.dev/

Gunakan metode instalasi terbaru yang tersedia pada dokumentasi Pi.

Setelah instalasi selesai, verifikasi:

```bash
pi --version
```

Untuk melihat opsi yang tersedia:

```bash
pi --help
```

---

# 4. Instalasi Package Pi

Daftar package Pi tersedia pada:

https://pi.dev/packages

Package yang direkomendasikan:

| Package                   | Fungsi                                                                  |
| ------------------------- | ----------------------------------------------------------------------- |
| `pi-web-access`           | Memberikan kemampuan untuk mengakses informasi dan dokumentasi dari web |
| `context-mode`            | Membantu mengelola context agar penggunaan token lebih efisien          |
| `dietrichgebert/ponytail` | Menambahkan kemampuan tambahan untuk workflow agent                     |
| `pi-soly`                 | Menambahkan fitur tambahan untuk workflow Pi                            |

Install setiap package menggunakan metode instalasi yang tersedia pada halaman package masing-masing.

> Nama package, syntax instalasi, konfigurasi, dan kompatibilitas dapat berubah. Gunakan dokumentasi resmi Pi Packages sebagai sumber utama.

Untuk penggunaan awal, rekomendasi setup:

```text
Pi Agent
├── context-mode
├── pi-web-access
└── RTK
```

Tambahkan `ponytail`, `pi-soly`, atau package lain secara bertahap sesuai kebutuhan.

Hindari memasang terlalu banyak package sejak awal. Semakin banyak tools, skills, dan plugins yang aktif, semakin banyak informasi yang berpotensi dimasukkan ke context model.

---

# 5. Konfigurasi Koneksi Langsung ke GGAI

Buat atau edit file konfigurasi Pi.

### Linux dan macOS

```text
~/.pi/agent/models.json
```

### Windows

```text
C:\Users\<USERNAME>\.pi\agent\models.json
```

Jika folder belum tersedia pada Linux atau macOS:

```bash
mkdir -p ~/.pi/agent
```

Buka menggunakan Nano:

```bash
nano ~/.pi/agent/models.json
```

Atau menggunakan Visual Studio Code:

```bash
code ~/.pi/agent/models.json
```

Masukkan konfigurasi:

```json
{
  "providers": {
    "litellm": {
      "baseUrl": "https://ggai-developer.geekdevops.my.id/v1",
      "api": "openai-completions",
      "apiKey": "DAPATKAN_DARI_ADMIN",
      "models": [
        {
          "id": "ornith-9b",
          "name": "Ornith 9B",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        },
        {
          "id": "deepseek-r1",
          "name": "DeepSeek R1",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        }
      ]
    }
  }
}
```

Ganti:

```text
DAPATKAN_DARI_ADMIN
```

dengan API key yang diberikan administrator.

> Nama model pada bagian `id` harus sama dengan nama model yang tersedia pada endpoint GGAI.

Untuk melihat daftar model:

```bash
curl https://ggai-developer.geekdevops.my.id/v1/models \
  -H "Authorization: Bearer MASUKKAN_API_KEY"
```

Pastikan ID DeepSeek disesuaikan dengan model yang tersedia. Jika model yang tersedia adalah `deepseek-v4`, gunakan ID tersebut.

---

# 6. Validasi Endpoint GGAI

Jalankan:

```bash
curl https://ggai-developer.geekdevops.my.id/v1/models \
  -H "Authorization: Bearer MASUKKAN_API_KEY"
```

Jika berhasil, endpoint akan menampilkan daftar model.

Jika mendapatkan:

```text
401 Unauthorized
```

periksa API key.

Jika mendapatkan:

```text
Connection refused
```

atau:

```text
Could not resolve host
```

periksa:

* Internet
* DNS
* VPN
* Proxy
* Firewall
* Status endpoint GGAI

---

# 7. Instalasi RTK

RTK digunakan untuk membantu mengoptimalkan output command sebelum dikirim ke AI.

RTK dapat membuat output menjadi lebih ringkas dan relevan sehingga membantu mengurangi penggunaan context dan token.

Repository dan dokumentasi resmi:

https://github.com/rtk-ai/rtk

> Instalasi RTK dapat berbeda tergantung operating system, arsitektur perangkat, dan versi RTK. Ikuti metode instalasi terbaru pada dokumentasi resmi RTK.

Pilih metode instalasi sesuai perangkat:

* Linux
* macOS Intel
* macOS Apple Silicon
* Windows
* Metode lain yang tersedia pada dokumentasi resmi

Setelah instalasi, verifikasi:

```bash
rtk --version
```

Lihat opsi:

```bash
rtk --help
```

## Penggunaan RTK

RTK berguna untuk output command panjang seperti:

```bash
docker logs
```

```bash
docker compose logs
```

```bash
kubectl logs
```

```bash
git diff
```

```bash
git status
```

```bash
docker ps
```

```bash
journalctl
```

Gunakan syntax dan contoh terbaru dari dokumentasi resmi:

https://github.com/rtk-ai/rtk

RTK direkomendasikan untuk:

* Analisis log Docker
* Analisis Docker Compose
* Analisis Git diff
* Debugging Kubernetes
* Analisis error aplikasi
* Analisis service Linux
* Analisis output build
* Analisis hasil testing
* Output terminal yang panjang

Perbedaan RTK dan `context-mode`:

| Tools          | Fungsi                                                    |
| -------------- | --------------------------------------------------------- |
| RTK            | Mengoptimalkan output command sebelum masuk ke context AI |
| `context-mode` | Membantu mengelola penggunaan context selama sesi Pi      |

Keduanya dapat digunakan secara bersamaan.

---

# 8. Menjalankan Pi pada Project

Masuk ke folder project:

```bash
cd /path/ke/project
```

Contoh:

```bash
cd ~/project/backend-api
```

Jalankan:

```bash
pi
```

Pi menggunakan folder aktif sebagai working directory.

## Menjalankan dari IDE

Buka project menggunakan Visual Studio Code, Cursor, Kiro, Zed, atau IDE lain.

Buka terminal bawaan IDE:

```text
Terminal → New Terminal
```

Pastikan berada pada root project:

```bash
pwd
```

Jalankan:

```bash
pi
```

---

# 9. Rekomendasi Penggunaan Model

Gunakan **Ornith 9B** sebagai model utama untuk:

* Membaca struktur project
* Analisis source code
* Membuat fungsi
* Membuat unit test
* Debugging
* Membuat dokumentasi
* Membuat konfigurasi
* Analisis Dockerfile
* Analisis Docker Compose
* Analisis Nginx
* Analisis log sederhana

Gunakan **DeepSeek V4** untuk:

* Debugging lintas banyak file
* Analisis arsitektur
* Root-cause analysis
* Refactoring kompleks
* Analisis dependency
* Reasoning kompleks

> DeepSeek V4 memiliki limit penggunaan. Gunakan Ornith 9B sebagai model utama dan gunakan DeepSeek ketika diperlukan.

---

# 10. Optimasi Context dan Token

Rekomendasi:

1. Gunakan Ornith 9B sebagai model utama.

2. Gunakan DeepSeek hanya untuk pekerjaan kompleks.

3. Jangan langsung meminta AI membaca seluruh repository.

Kurang direkomendasikan:

```text
Analisis semua source code pada repository ini.
```

Lebih spesifik:

```text
Analisis alur autentikasi pada folder src/auth dan cari kemungkinan penyebab token tidak tervalidasi.
```

4. Gunakan RTK untuk output command panjang.

5. Gunakan `context-mode`.

6. Mulai sesi baru jika berpindah pekerjaan.

7. Hindari memasukkan folder yang tidak diperlukan:

```text
node_modules
vendor
dist
build
coverage
.next
.nuxt
storage/logs
```

8. Gunakan file instruksi project jika didukung.

Contoh:

```text
Tech stack:
- Laravel 11
- PHP 8.3
- PostgreSQL
- Redis
- Docker Compose

Rules:
- Jangan mengubah production tanpa konfirmasi.
- Jangan menjalankan deployment tanpa konfirmasi.
- Jalankan test setelah perubahan.
- Jangan menampilkan secret.
- Jangan menghapus database atau volume.
```

---

# 11. Setup Opsional Menggunakan 9Router

9Router bersifat opsional.

Repository resmi:

https://github.com/decolua/9router.git

Gunakan 9Router jika membutuhkan:

* Endpoint AI terpusat
* Pengelolaan beberapa provider
* Pengelolaan API key
* Routing model
* Skills
* Plugins
* Provider tambahan
* Model dari langganan pribadi

## Provider atau Langganan AI Pribadi

9Router tidak hanya digunakan untuk GGAI.

Jika pengguna sudah memiliki langganan, API key, akun, atau akses ke provider lain, provider tersebut dapat ditambahkan ke 9Router masing-masing.

Contoh:

* OpenCode
* OpenAI
* Anthropic
* Google Gemini
* OpenRouter
* GitHub Copilot
* Provider OpenAI-compatible
* Provider lain yang didukung 9Router

Keuntungan:

* Mengelola beberapa provider melalui satu UI
* Menggunakan model kantor dan pribadi
* Mengakses beberapa provider melalui satu endpoint
* Menggunakan provider alternatif ketika provider utama mencapai limit
* Memilih model sesuai kebutuhan

> Setiap pengguna bertanggung jawab terhadap akun, API key, limit, biaya, kuota, dan ketentuan provider pribadi.

---

# 12. Instalasi 9Router

Pastikan Node.js tersedia:

```bash
node --version
npm --version
```

Clone repository:

```bash
git clone https://github.com/decolua/9router.git
```

Masuk ke folder:

```bash
cd 9router
```

Ikuti instalasi terbaru pada dokumentasi resmi 9Router.

## Menggunakan Docker

Instalasi Docker bersifat opsional.

Periksa Docker:

```bash
docker --version
docker compose version
```

Ikuti metode Docker yang tersedia pada repository resmi.

Periksa container:

```bash
docker compose ps
```

Periksa log:

```bash
docker compose logs -f
```

> Jangan mengekspos UI langsung ke internet tanpa authentication, HTTPS, firewall, VPN, atau reverse proxy.

---

# 13. Konfigurasi Awal 9Router

Setelah UI dapat diakses:

1. Buka UI 9Router.
2. Buat password administrator.
3. Gunakan password yang kuat.
4. Simpan password pada password manager.

---

# 14. Menambahkan GGAI ke 9Router

Masuk ke:

```text
Provider → Add Provider → OpenAI Compatible
```

Isi konfigurasi:

| Field    | Nilai                                        |
| -------- | -------------------------------------------- |
| Name     | GGAI                                         |
| Prefix   | Sesuaikan kebutuhan                          |
| Base URL | `https://ggai-developer.geekdevops.my.id/v1` |

Lakukan test koneksi.

Pastikan status:

```text
Valid
```

Simpan provider.

---

# 15. Menambahkan API Key GGAI

Buka provider GGAI.

Pilih:

```text
Add API Key
```

Masukkan API key yang diberikan administrator.

Pada bagian default model, gunakan:

```text
ornith-9b
```

Default model digunakan untuk memenuhi form konfigurasi.

Simpan API key dan pastikan status valid.

---

# 16. Import Model GGAI

Buka provider GGAI.

Pilih:

```text
Import from Model
```

Tunggu proses pengambilan model selesai.

Model yang tersedia pada GGAI akan masuk secara otomatis.

Contoh:

```text
ornith-9b
deepseek-v4
```

Salin nama model sesuai yang ditampilkan karena nama tersebut akan digunakan pada konfigurasi Pi.

---

# 17. Menambahkan Provider Pribadi

Untuk menambahkan provider lain:

1. Masuk ke menu `Provider`.
2. Pilih `Add Provider`.
3. Pilih provider yang digunakan.
4. Masukkan credential.
5. Masukkan API URL jika diperlukan.
6. Masukkan API key atau token.
7. Lakukan test koneksi.
8. Simpan provider.
9. Import model jika tersedia.
10. Tambahkan model ke endpoint 9Router.

Contoh konfigurasi OpenCode:

```text
Provider:
OpenCode

Endpoint:
SESUAI_ENDPOINT_OPENCODE

API Key:
API_KEY_PRIBADI

Model:
SESUAI_MODEL_YANG_TERSEDIA
```

> Gunakan dokumentasi provider masing-masing karena endpoint, authentication, dan nama model dapat berbeda.

---

# 18. Membuat Endpoint 9Router

Masuk ke menu:

```text
Endpoint
```

Buat endpoint baru.

Aktifkan skills dan plugins yang dibutuhkan.

Jika ingin seluruh kemampuan 9Router tersedia, seluruh skills dan plugins dapat diaktifkan.

Namun, untuk penggunaan lebih hemat context, aktifkan fitur yang diperlukan saja.

Kemudian:

1. Simpan endpoint.
2. Buat API key.
3. Salin endpoint URL.
4. Salin API key.

Contoh:

```text
Endpoint:
http://localhost:<PORT>/v1

API Key:
sk-xxxxxxxxxxxxxxxx
```

---

# 19. Menghubungkan Pi ke 9Router

Edit:

```text
~/.pi/agent/models.json
```

Contoh:

```json
{
  "providers": {
    "9router": {
      "baseUrl": "URL_ENDPOINT_9ROUTER",
      "api": "openai-completions",
      "apiKey": "API_KEY_9ROUTER",
      "models": [
        {
          "id": "ornith-9b",
          "name": "Ornith 9B",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        },
        {
          "id": "NAMA_MODEL_OPENCODE",
          "name": "OpenCode Model",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        }
      ]
    }
  }
}
```

Sesuaikan:

```text
URL_ENDPOINT_9ROUTER
API_KEY_9ROUTER
NAMA_MODEL_OPENCODE
```

Nama model harus sama dengan model yang tersedia pada endpoint 9Router.

---

# 20. Menambahkan Provider Langsung ke Pi

Provider pribadi tidak wajib dimasukkan ke 9Router.

Provider baru dapat ditambahkan langsung ke:

```text
~/.pi/agent/models.json
```

Contoh GGAI dan OpenCode:

```json
{
  "providers": {
    "ggai": {
      "baseUrl": "https://ggai-developer.geekdevops.my.id/v1",
      "api": "openai-completions",
      "apiKey": "API_KEY_GGAI",
      "models": [
        {
          "id": "ornith-9b",
          "name": "Ornith 9B",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        }
      ]
    },
    "opencode": {
      "baseUrl": "URL_OPENCODE",
      "api": "openai-completions",
      "apiKey": "API_KEY_OPENCODE",
      "models": [
        {
          "id": "NAMA_MODEL_OPENCODE",
          "name": "OpenCode Model",
          "reasoning": true,
          "input": ["text"],
          "contextWindow": 64000,
          "maxTokens": 8192,
          "cost": {
            "input": 0,
            "output": 0,
            "cacheRead": 0,
            "cacheWrite": 0
          }
        }
      ]
    }
  }
}
```

Sesuaikan:

```text
URL_OPENCODE
API_KEY_OPENCODE
NAMA_MODEL_OPENCODE
```

dengan informasi provider yang digunakan.

---

# 21. Menjalankan Pi melalui 9Router

Masuk ke folder project:

```bash
cd ~/project/nama-project
```

Jalankan:

```bash
pi
```

Alur koneksi:

```text
Pi
 │
 ▼
9Router
 │
 ├── GGAI
 ├── OpenCode
 ├── OpenAI
 ├── Anthropic
 └── Provider Lain
```

Lakukan pengujian:

```text
Analisis struktur project ini tanpa melakukan perubahan file. Jelaskan teknologi yang digunakan dan file utama yang perlu diperhatikan.
```

---

# 22. Checklist Instalasi

Setup dasar:

```text
[ ] Endpoint GGAI sudah diterima
[ ] API key GGAI sudah diterima
[ ] Node.js sudah terpasang
[ ] npm sudah tersedia
[ ] Pi Agent sudah terpasang
[ ] Package Pi sudah terpasang
[ ] models.json sudah dibuat
[ ] Ornith 9B berhasil digunakan
[ ] DeepSeek berhasil digunakan
[ ] RTK sudah terpasang
[ ] Pi berhasil berjalan dari folder project
```

Setup tambahan menggunakan 9Router:

```text
[ ] 9Router sudah terpasang
[ ] UI dapat diakses
[ ] Password administrator sudah dibuat
[ ] GGAI sudah ditambahkan
[ ] API key GGAI sudah ditambahkan
[ ] Provider berhasil diuji
[ ] Model berhasil di-import
[ ] Provider pribadi ditambahkan jika diperlukan
[ ] Endpoint sudah dibuat
[ ] Skills dan plugins sudah dipilih
[ ] API key endpoint sudah dibuat
[ ] Pi berhasil terhubung ke 9Router
```

---

# 23. Troubleshooting

## Pi Tidak Ditemukan

Error:

```text
command not found: pi
```

Periksa:

```bash
which pi
npm list -g --depth=0
```

Windows:

```powershell
where.exe pi
```

## API Key Tidak Valid

Error:

```text
401 Unauthorized
```

Periksa:

* API key
* Masa aktif API key
* Endpoint
* Spasi tambahan
* Environment yang digunakan

## Model Tidak Ditemukan

Error:

```text
Model not found
```

Pastikan:

```json
"id": "NAMA_MODEL"
```

sama dengan nama model pada GGAI, 9Router, atau provider yang digunakan.

## JSON Tidak Valid

Validasi:

```bash
jq . ~/.pi/agent/models.json
```

Periksa:

* Koma
* Kurung
* Tanda kutip
* Struktur JSON

---

# 24. Keamanan

Jangan menyimpan API key pada:

```text
README.md
GitHub Issue
GitHub Wiki publik
Screenshot
Source code
Git commit
Chat publik
```

Sebelum melakukan commit:

```bash
git diff
```

Cari kemungkinan secret:

```bash
git grep -n "sk-"
```

Jika API key tidak sengaja bocor:

1. Segera revoke atau nonaktifkan API key.
2. Buat API key baru.
3. Hapus secret dari repository.
4. Bersihkan Git history jika diperlukan.

---

# 25. Alur Setup

Setup dasar:

```text
Dapatkan Endpoint dan API Key
              │
              ▼
       Install Node.js
              │
              ▼
        Install Pi Agent
              │
              ▼
      Install Package Pi
              │
              ▼
    Konfigurasi models.json
              │
              ▼
       Test Ornith 9B
              │
              ▼
         Install RTK
              │
              ▼
Jalankan Pi dari Folder Project
```

Setup menggunakan 9Router:

```text
Install 9Router
       │
       ▼
Tambahkan GGAI
       │
       ├── Tambahkan Provider Pribadi
       │
       ▼
Import Model
       │
       ▼
Buat Endpoint
       │
       ▼
Pilih Skills dan Plugins
       │
       ▼
Buat API Key
       │
       ▼
Hubungkan Pi
```

---

# Penutup

Setup yang direkomendasikan:

```text
Pi Agent
├── Ornith 9B
│   └── Model utama untuk coding
│
├── DeepSeek V4
│   └── Reasoning kompleks dengan limit
│
├── context-mode
│   └── Pengelolaan context
│
├── pi-web-access
│   └── Akses dokumentasi terbaru
│
├── RTK
│   └── Optimasi output dan penggunaan token
│
└── 9Router (Opsional)
    ├── GGAI
    ├── OpenCode
    ├── OpenAI
    ├── Anthropic
    ├── Google Gemini
    ├── OpenRouter
    └── Provider lain
```

Untuk setup awal, gunakan koneksi langsung dari Pi ke GGAI terlebih dahulu.

Jika sudah memiliki langganan provider lain, provider tersebut dapat ditambahkan ke 9Router masing-masing atau ditambahkan langsung sebagai provider baru pada file `.pi/agent/models.json`.

Pastikan URL endpoint, API key, nama model, context window, dan maximum output token disesuaikan dengan provider yang digunakan.
