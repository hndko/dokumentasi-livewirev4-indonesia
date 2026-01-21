# Step 5: Setup Layout & Route

## 🎯 Tujuan

- Membuat layout untuk aplikasi
- Mendaftarkan route
- Menghubungkan komponen ke halaman

---

## 📝 Langkah 5.1: Buat Folder Layout

```bash
mkdir -p resources/views/components/layouts
```

---

## 📝 Langkah 5.2: Buat File Layout

Buat file `resources/views/components/layouts/app.blade.php`:

```html
<!DOCTYPE html>
<html lang="id">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{ $title ?? 'Todo App' }}</title>

    <!-- Tailwind CSS via CDN (untuk development) -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Custom Styles -->
    <style>
      body {
        font-family: "Inter", system-ui, sans-serif;
      }
    </style>
  </head>
  <body class="bg-gradient-to-br from-blue-50 to-indigo-100 min-h-screen">
    <!-- Header -->
    <header class="bg-white shadow-sm">
      <div class="max-w-4xl mx-auto px-4 py-4">
        <h1 class="text-xl font-bold text-gray-800">⚡ Livewire Todo App</h1>
      </div>
    </header>

    <!-- Main Content -->
    <main class="py-8">{{ $slot }}</main>

    <!-- Footer -->
    <footer
      class="fixed bottom-0 w-full py-4 text-center text-gray-500 text-sm"
    >
      Built with Laravel & Livewire v4
    </footer>
  </body>
</html>
```

### Penjelasan:

| Bagian                       | Fungsi                          |
| ---------------------------- | ------------------------------- |
| `{{ $slot }}`                | Tempat konten komponen dirender |
| `{{ $title ?? 'Todo App' }}` | Title dengan default value      |
| Tailwind CDN                 | Styling cepat untuk development |

---

## 📝 Langkah 5.3: Setup Route

Buka `routes/web.php` dan ubah menjadi:

```php
<?php

use App\Livewire\TodoList;
use Illuminate\Support\Facades\Route;

// Route ke komponen Livewire langsung
Route::get('/', TodoList::class);
```

### Cara Alternatif (dengan view biasa):

```php
Route::get('/', function () {
    return view('welcome');
});
```

Lalu ubah `resources/views/welcome.blade.php`:

```html
<x-layouts.app>
  <livewire:todo-list />
</x-layouts.app>
```

---

## 📝 Langkah 5.4: Konfigurasi Livewire (Opsional)

Jika ingin mempublish konfigurasi:

```bash
php artisan livewire:publish --config
```

File konfigurasi akan ada di `config/livewire.php`.

Beberapa opsi penting:

```php
return [
    // Default layout
    'layout' => 'components.layouts.app',

    // Inject assets otomatis
    'inject_assets' => true,

    // Pagination theme
    'pagination_theme' => 'tailwind',
];
```

---

## 📝 Langkah 5.5: Jalankan Server

```bash
php artisan serve
```

---

## 📝 Langkah 5.6: Test di Browser

Buka `http://localhost:8000`

Anda akan melihat:

- Header dengan judul app
- Form input todo
- Area daftar todo (kosong)
- Footer

---

## 🔍 Troubleshooting

### Error: View not found

Pastikan struktur folder benar:

```
resources/
└── views/
    ├── components/
    │   └── layouts/
    │       └── app.blade.php
    └── livewire/
        └── todo-list.blade.php
```

### Error: Class not found

```bash
composer dump-autoload
```

### Styles tidak muncul

Pastikan Tailwind CDN terload dengan memeriksa Network tab di browser DevTools.

---

## ✅ Checkpoint

Setelah step ini, Anda harus memiliki:

- [x] Layout dengan header dan footer
- [x] Route terkonfigurasi
- [x] Aplikasi berjalan di browser

---

## ⬅️ [Step 4: View Komponen](04-view-komponen.md) | ➡️ [Step 6: Testing & Finalisasi](06-testing-finalisasi.md)
