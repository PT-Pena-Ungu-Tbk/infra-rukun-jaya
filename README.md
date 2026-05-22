# 🐳 Infra Rukun Jaya - Orchestration Setup

Repository ini adalah pusat kontrol untuk menjalankan seluruh ekosistem **Sistem Kasir Toko Rukun Jaya** secara lokal. Kita menggunakan Docker Compose untuk mengorkestrasi Frontend (Next.js), Backend (Express.js), dan Database (PostgreSQL) secara serentak agar seluruh tim bisa *ngoding* di environment yang seragam.

### 🏗️ Arsitektur Sistem Lokal

| Service | Teknologi | Port |
| :--- | :--- | :--- |
| **Frontend** | Next.js (TypeScript) | `3000` |
| **Backend API** | Express.js (Node.js) | `5000` |
| **Database** | PostgreSQL | `5432` |

---

### 🛑 Prasyarat

Sebelum memulai, pastikan sistem kamu sudah memenuhi syarat berikut:
* **Docker Desktop** terinstal dan mesin Docker dalam keadaan *running*.
* **Git** terinstal.

*(Catatan: Kamu tidak perlu menginstal Node.js atau PostgreSQL secara manual di komputermu, semuanya akan dijalankan di dalam container Docker).*

---

### 📂 1. Standar Struktur Folder

Sistem ini menggunakan pendekatan Multi-repo. Semua repository harus berada di dalam satu folder induk (*workspace*) yang sejajar (*sibling*).

Buat sebuah folder kosong (misal: `rukun-jaya-workspace`), lalu lakukan clone sehingga strukturnya persis seperti ini:

```text
📁 rukun-jaya-workspace/
 ┣ 📁 be-rukun-jaya/       <-- Repository Backend (Express)
 ┣ 📁 fe-rukun-jaya/       <-- Repository Frontend (Next.js)
 ┗ 📁 infra-rukun-jaya/    <-- Repository ini (Pusat Compose)
```

**Jalan Pintas Cloning** — Jalankan di dalam folder workspace-mu:
```bash
git clone [https://github.com/PT-Pena-Ungu-Tbk/infra-rukun-jaya.git](https://github.com/PT-Pena-Ungu-Tbk/infra-rukun-jaya.git) && \
git clone [https://github.com/PT-Pena-Ungu-Tbk/be-rukun-jaya.git](https://github.com/PT-Pena-Ungu-Tbk/be-rukun-jaya.git) && \
git clone [https://github.com/PT-Pena-Ungu-Tbk/fe-rukun-jaya.git](https://github.com/PT-Pena-Ungu-Tbk/fe-rukun-jaya.git)
```

---

### ⚙️ 2. Konfigurasi Environment

Karena semua *service* (termasuk Database) berjalan di dalam jaringan internal Docker, kamu perlu menyesuaikan file `.env` di Backend.

Masuk ke folder `be-rukun-jaya`, gandakan file `.env.example` menjadi `.env`, lalu atur koneksi database-nya seperti ini:

```env
DB_USER=postgres
DB_PASSWORD=password_rahasia
DB_HOST=db                 # Wajib menggunakan nama service Docker, bukan localhost
DB_PORT=5432
DB_NAME=rukun_jaya_db
PORT=5000
```

---

### 🚀 3. Cara Menjalankan Ekosistem

Jika struktur folder sudah benar dan `.env` Backend sudah diatur, ikuti langkah berikut:

1.  Buka terminal dan pastikan kamu berada di dalam direktori `infra-rukun-jaya`:
    ```bash
    cd infra-rukun-jaya
    ```
2.  Jalankan perintah berikut untuk menyalakan semua container:
    ```bash
    docker compose up
    ```
3.  Tunggu hingga terminal menampilkan log bahwa server Express dan Next.js sudah siap.

---

### 🌍 4. Titik Akses (Access Points)

Setelah semua service menyala, akses melalui browser atau Postman:

* 🖥️ **Frontend (Next.js):** `http://localhost:3000`
* ⚙️ **Backend API (Express):** `http://localhost:5000`
* 🗄️ **Database (PostgreSQL):** `localhost:5432` *(bisa diakses via DBeaver/PgAdmin menggunakan kredensial di atas)*

---

### 🛠️ Perintah Berguna (Cheatsheet)

* **Mematikan semua service (Aman):**
    Tekan `Ctrl + C` di terminal tempat Docker Compose berjalan, atau jalankan:
    ```bash
    docker compose down
    ```
* **Membersihkan container & menghapus volume (Hard Reset - Data DB akan hilang):**
    ```bash
    docker compose down -v
    ```
* **Menjalankan perintah Prisma Migrate di dalam container Backend:**
    Buka terminal baru di folder `infra-rukun-jaya`, lalu ketik:
    ```bash
    docker compose exec backend npx prisma migrate dev
    ```