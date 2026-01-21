# Step 1: Setup Project & Database

## 🎯 Tujuan

- Setup project Laravel dengan Livewire
- Membuat migrations untuk posts dan comments
- Konfigurasi storage untuk upload

---

## 📝 Langkah 1.1: Buat Project

```bash
composer create-project laravel/laravel blog-app
cd blog-app
composer require livewire/livewire
```

---

## 📝 Langkah 1.2: Konfigurasi .env

```env
APP_NAME="Blog App"
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=blog_app
DB_USERNAME=root
DB_PASSWORD=
```

---

## 📝 Langkah 1.3: Buat Database

```sql
CREATE DATABASE blog_app;
```

---

## 📝 Langkah 1.4: Migration Posts

```bash
php artisan make:migration create_posts_table
```

Edit file migration:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->string('slug')->unique();
            $table->text('content');
            $table->string('image')->nullable();
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

---

## 📝 Langkah 1.5: Migration Comments

```bash
php artisan make:migration create_comments_table
```

Edit file migration:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->text('body');
            $table->foreignId('post_id')->constrained()->onDelete('cascade');
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('comments');
    }
};
```

---

## 📝 Langkah 1.6: Jalankan Migrations

```bash
php artisan migrate
```

---

## 📝 Langkah 1.7: Setup Storage Link

Untuk file uploads:

```bash
php artisan storage:link
```

---

## 📝 Langkah 1.8: Install Authentication (Opsional tapi Direkomendasikan)

```bash
composer require laravel/breeze --dev
php artisan breeze:install blade
npm install && npm run build
php artisan migrate
```

---

## ✅ Checkpoint

- [x] Project Laravel + Livewire terinstall
- [x] Database terkonfigurasi
- [x] Tabel posts dan comments sudah ada
- [x] Storage link untuk uploads

---

## ➡️ Lanjut ke [Step 2: Models & Relationships](02-models-relationships.md)
