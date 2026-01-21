# Step 2: Dashboard dengan Islands

## 🎯 Tujuan

- Membuat dashboard dengan islands
- Memahami isolated rendering

---

## 📝 Langkah 2.1: Generate Dashboard

```bash
php artisan make:livewire Dashboard
```

---

## 📝 Langkah 2.2: Dashboard Component

```php
<?php
// app/Livewire/Dashboard.php

namespace App\Livewire;

use Livewire\Attributes\Layout;
use Livewire\Attributes\Title;
use Livewire\Component;

#[Layout('components.layouts.app')]
#[Title('Dashboard Analytics')]
class Dashboard extends Component
{
    public function render()
    {
        return view('livewire.dashboard');
    }
}
```

---

## 📝 Langkah 2.3: Dashboard View dengan Islands

```html
<!-- resources/views/livewire/dashboard.blade.php -->
<div class="p-6">
  <h1 class="text-3xl font-bold mb-6">📊 Dashboard</h1>

  <!-- Stats Grid dengan Islands (lazy loaded) -->
  <div class="grid md:grid-cols-4 gap-4 mb-8">
    @island(lazy: true)
    <livewire:stats.revenue-card />
    @endisland @island(lazy: true)
    <livewire:stats.users-card />
    @endisland @island(lazy: true)
    <livewire:stats.orders-card />
    @endisland @island(lazy: true)
    <livewire:stats.conversion-card />
    @endisland
  </div>

  <!-- Charts -->
  <div class="grid md:grid-cols-2 gap-6">
    @island(name: 'sales-chart')
    <livewire:charts.sales-chart />
    @endisland @island(name: 'traffic-chart')
    <livewire:charts.traffic-chart />
    @endisland
  </div>
</div>
```

---

## 📝 Penjelasan Islands

| Parameter     | Deskripsi                     |
| ------------- | ----------------------------- |
| `lazy: true`  | Load saat visible di viewport |
| `defer: true` | Load setelah page load        |
| `name: 'x'`   | Identifier untuk targeting    |

---

## ✅ Checkpoint

- [x] Dashboard dengan island structure
- [x] Lazy loading islands

---

## ⬅️ [Step 1](01-setup-architecture.md) | ➡️ [Step 3: Polling](03-stats-polling.md)
