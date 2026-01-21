# Step 2: Membuat Database & Model

## 🎯 Tujuan

- Membuat migration untuk tabel todos
- Membuat model Todo
- Menjalankan migration

---

## 📝 Langkah 2.1: Buat Migration

Jalankan perintah Artisan untuk membuat migration:

```bash
php artisan make:migration create_todos_table
```

Ini akan membuat file di `database/migrations/xxxx_xx_xx_xxxxxx_create_todos_table.php`

---

## 📝 Langkah 2.2: Edit Migration

Buka file migration yang baru dibuat dan ubah method `up()`:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('todos', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->boolean('completed')->default(false);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('todos');
    }
};
```

### Penjelasan Kolom:

| Kolom        | Tipe     | Deskripsi                       |
| ------------ | -------- | ------------------------------- |
| `id`         | bigint   | Primary key, auto increment     |
| `title`      | string   | Judul todo                      |
| `completed`  | boolean  | Status selesai (default: false) |
| `timestamps` | datetime | created_at & updated_at         |

---

## 📝 Langkah 2.3: Jalankan Migration

```bash
php artisan migrate
```

Output yang diharapkan:

```
INFO  Running migrations.
2024_01_01_000000_create_todos_table ............. DONE
```

---

## 📝 Langkah 2.4: Buat Model Todo

```bash
php artisan make:model Todo
```

---

## 📝 Langkah 2.5: Edit Model Todo

Buka `app/Models/Todo.php` dan tambahkan `$fillable`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Todo extends Model
{
    /**
     * Kolom yang boleh diisi secara mass assignment
     */
    protected $fillable = [
        'title',
        'completed',
    ];

    /**
     * Cast kolom ke tipe data tertentu
     */
    protected $casts = [
        'completed' => 'boolean',
    ];
}
```

### Penjelasan:

- `$fillable`: Daftar kolom yang boleh diisi menggunakan `create()` atau `update()`
- `$casts`: Otomatis konversi tipe data (0/1 menjadi true/false)

---

## 📝 Langkah 2.6: Test Model (Opsional)

Buka Tinker untuk test:

```bash
php artisan tinker
```

Coba buat todo:

```php
>>> App\Models\Todo::create(['title' => 'Belajar Livewire'])
=> App\Models\Todo {
     title: "Belajar Livewire",
     completed: false,
     updated_at: "2024-01-21 10:00:00",
     created_at: "2024-01-21 10:00:00",
     id: 1,
   }

>>> App\Models\Todo::all()
=> Illuminate\Database\Eloquent\Collection {
     all: [
       App\Models\Todo {...},
     ],
   }

>>> exit
```

---

## ✅ Checkpoint

Setelah step ini, Anda harus memiliki:

- [x] Tabel `todos` di database
- [x] Model `Todo` dengan fillable dan casts
- [x] Data test (opsional)

---

## ⬅️ [Step 1: Setup Project](01-setup-project.md) | ➡️ [Step 3: Komponen Livewire](03-komponen-livewire.md)
