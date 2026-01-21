# Step 1: Setup Project & Architecture

## 🎯 Tujuan

- Setup project untuk dashboard
- Memahami arsitektur islands

---

## 📝 Langkah 1.1: Buat Project

```bash
composer create-project laravel/laravel dashboard-app
cd dashboard-app
composer require livewire/livewire
npm install chart.js
```

---

## 📝 Langkah 1.2: Migration Orders (untuk demo data)

```bash
php artisan make:migration create_orders_table
```

```php
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->decimal('total', 10, 2);
    $table->string('status'); // pending, completed, cancelled
    $table->timestamps();
});
```

---

## 📝 Langkah 1.3: Model & Seeder

```php
// app/Models/Order.php
class Order extends Model
{
    protected $fillable = ['total', 'status'];
}
```

```php
// database/seeders/DatabaseSeeder.php
for ($i = 0; $i < 100; $i++) {
    Order::create([
        'total' => rand(10000, 1000000),
        'status' => ['pending', 'completed', 'cancelled'][rand(0, 2)],
        'created_at' => now()->subDays(rand(0, 30)),
    ]);
}
```

```bash
php artisan migrate --seed
```

---

## 📝 Langkah 1.4: Arsitektur Islands

```
Dashboard
├── @island(lazy) → RevenueCard
├── @island(lazy) → UsersCard
├── @island(lazy) → OrdersCard
└── @island(name: 'chart') → SalesChart
```

Setiap island render secara independen!

---

## ✅ Checkpoint

- [x] Project setup
- [x] Demo data tersedia
- [x] Memahami arsitektur

---

## ➡️ [Step 2: Dashboard Islands](02-dashboard-islands.md)
