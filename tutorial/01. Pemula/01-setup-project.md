# Step 1: Setup Project Laravel + Livewire

## 🎯 Tujuan

- Membuat project Laravel baru
- Menginstall Livewire v4
- Memahami struktur project

---

## 📝 Langkah 1.1: Buat Project Laravel Baru

Buka terminal dan jalankan perintah berikut:

```bash
composer create-project laravel/laravel todo-app
```

Tunggu sampai proses selesai. Ini akan membuat folder `todo-app` dengan struktur Laravel lengkap.

---

## 📝 Langkah 1.2: Masuk ke Folder Project

```bash
cd todo-app
```

---

## 📝 Langkah 1.3: Install Livewire

```bash
composer require livewire/livewire
```

Livewire akan otomatis terinstall sebagai dependency Laravel.

---

## 📝 Langkah 1.4: Verifikasi Instalasi

Jalankan server development:

```bash
php artisan serve
```

Buka browser dan akses `http://localhost:8000`. Anda akan melihat halaman welcome Laravel.

---

## 📝 Langkah 1.5: Konfigurasi Database

Buka file `.env` dan sesuaikan konfigurasi database:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=todo_app
DB_USERNAME=root
DB_PASSWORD=
```

> **Tips:** Jika menggunakan SQLite, ubah menjadi:
>
> ```env
> DB_CONNECTION=sqlite
> ```
>
> Dan buat file database:
>
> ```bash
> touch database/database.sqlite
> ```

---

## 📝 Langkah 1.6: Membuat Database

Jika menggunakan MySQL, buat database:

```sql
CREATE DATABASE todo_app;
```

---

## ✅ Checkpoint

Setelah step ini, Anda harus memiliki:

- [x] Project Laravel baru di folder `todo-app`
- [x] Livewire terinstall
- [x] Database terkonfigurasi
- [x] Server development berjalan

---

## ➡️ Lanjut ke [Step 2: Database & Model](02-database-model.md)
