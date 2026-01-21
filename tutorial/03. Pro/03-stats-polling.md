# Step 3: Stats Cards dengan Polling

## 🎯 Tujuan

- Membuat stats card yang auto-refresh
- Menggunakan computed properties dengan caching

---

## 📝 Langkah 3.1: Generate Stats Component

```bash
php artisan make:livewire Stats/RevenueCard
```

---

## 📝 Langkah 3.2: RevenueCard Component

```php
<?php
// app/Livewire/Stats/RevenueCard.php

namespace App\Livewire\Stats;

use App\Models\Order;
use Livewire\Attributes\Computed;
use Livewire\Component;

class RevenueCard extends Component
{
    #[Computed(persist: true, seconds: 60)]
    public function revenue()
    {
        return Order::whereDate('created_at', today())->sum('total');
    }

    #[Computed]
    public function change()
    {
        $yesterday = Order::whereDate('created_at', today()->subDay())->sum('total');
        if ($yesterday == 0) return 0;
        return round((($this->revenue - $yesterday) / $yesterday) * 100, 1);
    }

    public function render()
    {
        return view('livewire.stats.revenue-card');
    }
}
```

---

## 📝 Langkah 3.3: RevenueCard View dengan Polling

```html
<!-- resources/views/livewire/stats/revenue-card.blade.php -->
<div wire:poll.10s class="bg-white p-6 rounded-lg shadow">
  @placeholder
  <div class="animate-pulse">
    <div class="h-4 bg-gray-200 rounded w-1/2 mb-2"></div>
    <div class="h-8 bg-gray-200 rounded w-3/4"></div>
  </div>
  @endplaceholder

  <p class="text-gray-500 text-sm">Revenue Hari Ini</p>
  <p class="text-2xl font-bold">Rp {{ number_format($this->revenue) }}</p>

  <span class="{{ $this->change >= 0 ? 'text-green-500' : 'text-red-500' }}">
    {{ $this->change >= 0 ? '↑' : '↓' }} {{ abs($this->change) }}%
  </span>
</div>
```

---

## 📝 Penjelasan

| Konsep                       | Deskripsi                    |
| ---------------------------- | ---------------------------- |
| `wire:poll.10s`              | Auto-refresh setiap 10 detik |
| `#[Computed(persist: true)]` | Cache di session             |
| `@placeholder`               | Skeleton saat lazy loading   |

---

## ✅ Checkpoint

- [x] Stats card dengan real-time polling
- [x] Computed properties dengan caching

---

## ⬅️ [Step 2](02-dashboard-islands.md) | ➡️ [Step 4: Charts](04-charts-javascript.md)
