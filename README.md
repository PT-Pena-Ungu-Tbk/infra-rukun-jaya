# 🐳 Infra Rukun Jaya - Orchestration Setup

Repository ini adalah pusat kontrol untuk menjalankan seluruh ekosistem **Sistem Kasir Toko Bangunan Rukun Jaya** secara lokal menggunakan **Docker Compose**. Kita mengorkestrasi Frontend (Next.js), Backend (Express.js + TypeScript), dan Database (PostgreSQL) secara serentak agar seluruh tim memiliki environment pengembangan yang seragam.

---

### 🏗️ Arsitektur Sistem Lokal

| Service | Teknologi | Port Eksternal | Port Internal | Deskripsi |
| :--- | :--- | :--- | :--- | :--- |
| **Database** | PostgreSQL 15 (Alpine) | `5432` | `5432` | Database utama toko |
| **Backend API** | Express.js & Prisma (TypeScript) | `5000` | `5000` | REST API (di-build via Dockerfile backend) |
| **Frontend** | Next.js (TypeScript) | `3000` | `3000` | Dashboard/Kasir UI |

---

### 🛑 Prasyarat

Sebelum memulai, pastikan sistem Anda sudah memenuhi syarat berikut:
* **Docker Desktop** terinstal dan mesin Docker (*Docker Engine*) dalam keadaan berjalan (*running*).
* **Git** terinstal.

*(Catatan: Anda tidak perlu menginstal Node.js, npm, atau PostgreSQL secara manual di komputer lokal. Seluruh proses build, migrasi, dan eksekusi berjalan otomatis di dalam kontainer).*

---

### 📂 1. Standar Struktur Folder

Sistem ini menggunakan pendekatan Multi-repo. Semua repository harus berada di dalam satu folder induk (*workspace*) yang sejajar (*sibling*).

Buat sebuah folder kosong (misal: `Sistem-Kasir-Toko-Rukun-Jaya`), lalu lakukan clone sehingga strukturnya persis seperti ini:

```text
📁 Sistem-Kasir-Toko-Rukun-Jaya/
 ┣ 📁 be-rukun-jaya/       <-- Repository Backend (Express)
 ┣ 📁 fe-rukun-jaya/       <-- Repository Frontend (Next.js)
 ┗ 📁 infra-rukun-jaya/    <-- Repository ini (Pusat Compose)
```

---

### ⚙️ 2. Konfigurasi Environment & Database

Saat dijalankan menggunakan Docker Compose:
- Konfigurasi database untuk Backend telah disediakan secara otomatis melalui blok `environment` di file `docker-compose.yml`.
- Alamat database (`DATABASE_URL`) diarahkan secara internal ke kontainer PostgreSQL menggunakan nama service-nya, yaitu **`db`**:
  `postgresql://postgres:password_rahasia@db:5432/rukun_jaya_db?schema=public`
- Anda dapat menyesuaikan `JWT_SECRET` atau variabel lainnya langsung di dalam file `docker-compose.yml` pada bagian environment backend.

---

### 🚀 3. Cara Menjalankan Ekosistem

Jika struktur folder sudah sesuai, silakan ikuti langkah berikut untuk mem-build dan menjalankan aplikasi:

1. Buka terminal (CMD / PowerShell / Bash) dan masuk ke direktori `infra-rukun-jaya`:
   ```bash
   cd infra-rukun-jaya
   ```

2. Jalankan perintah berikut untuk mengunduh image base, mem-build backend, dan menyalakan semua container di background:
   ```bash
   docker-compose up -d --build
   ```

3. Tunggu hingga proses build selesai. Anda dapat memantau log backend (untuk melihat apakah Prisma migration berjalan sukses) dengan perintah:
   ```bash
   docker-compose logs -f backend
   ```

---

### 🌍 4. Titik Akses (Access Points)

Setelah semua kontainer menyala:

* 🖥️ **Frontend (Next.js UI):** [http://localhost:3000](http://localhost:3000)
* ⚙️ **Backend API (Express):** [http://localhost:5000](http://localhost:5000)
* 📚 **Dokumentasi API (Scalar):** [http://localhost:5000/docs](http://localhost:5000/docs)
* 🗄️ **Database (PostgreSQL):** `localhost:5432` 
  *(dapat diakses menggunakan DBeaver/PgAdmin dengan User: `postgres`, Password: `password_rahasia`, Database: `rukun_jaya_db`)*

---

### 🛠️ Perintah Berguna (Cheatsheet)

* **Melihat Status Container:**
  ```bash
  docker-compose ps
  ```
* **Melihat Log Seluruh Service:**
  ```bash
  docker-compose logs -f
  ```
* **Mematikan Seluruh Container (Aman):**
  ```bash
  docker-compose down
  ```
* **Reset Database (Menghapus Volume & Data Permanen):**
  ```bash
  docker-compose down -v
  ```
* **Menjalankan Perintah di dalam Kontainer Backend secara interaktif (misal: Manual seeding ulang):**
  ```bash
  docker-compose exec backend npx prisma db seed
  ```