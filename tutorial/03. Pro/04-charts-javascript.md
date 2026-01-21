# Step 4: Charts dengan JavaScript Integration

## 🎯 Tujuan

- Integrasi Chart.js dengan Livewire
- Menggunakan @script dan $wire

---

## 📝 Langkah 4.1: Generate Chart Component

```bash
php artisan make:livewire Charts/SalesChart
```

---

## 📝 Langkah 4.2: SalesChart Component

```php
<?php
// app/Livewire/Charts/SalesChart.php

namespace App\Livewire\Charts;

use App\Models\Order;
use Livewire\Attributes\Computed;
use Livewire\Component;

class SalesChart extends Component
{
    public int $days = 7;

    #[Computed]
    public function chartData()
    {
        $data = collect();
        for ($i = $this->days - 1; $i >= 0; $i--) {
            $date = today()->subDays($i);
            $data->push([
                'date' => $date->format('d M'),
                'sales' => Order::whereDate('created_at', $date)->sum('total'),
            ]);
        }
        return $data;
    }

    public function render()
    {
        return view('livewire.charts.sales-chart');
    }
}
```

---

## 📝 Langkah 4.3: SalesChart View dengan @script

```html
<div class="bg-white p-6 rounded-lg shadow" wire:ignore>
  <div class="flex justify-between mb-4">
    <h3 class="font-bold">📈 Sales</h3>
    <select wire:model.live="days" class="border rounded px-2 py-1">
      <option value="7">7 Hari</option>
      <option value="14">14 Hari</option>
      <option value="30">30 Hari</option>
    </select>
  </div>

  <canvas id="salesChart" height="200"></canvas>

  @script
  <script>
    let chart = null;

    function renderChart(data) {
        const ctx = document.getElementById('salesChart');
        if (chart) chart.destroy();

        chart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: data.map(d => d.date),
                datasets: [{
                    label: 'Sales',
                    data: data.map(d => d.sales),
                    borderColor: '#3b82f6',
                    tension: 0.3,
                    fill: true,
                }]
            }
        });
    }

    // Initial render
    renderChart(@js($this->chartData));

    // Update saat data berubah
    Livewire.on('chartUpdated', (data) => renderChart(data));
  </script>
  @endscript
</div>
```

---

## 📝 Penjelasan

| Konsep        | Deskripsi                |
| ------------- | ------------------------ |
| `wire:ignore` | Jangan morph element ini |
| `@script`     | Script khusus komponen   |
| `@js($data)`  | Convert PHP ke JS        |

---

## ✅ Checkpoint

- [x] Chart.js integration
- [x] Script dalam komponen

---

## ⬅️ [Step 3](03-stats-polling.md) | ➡️ [Step 5: Drag-Drop](05-draggable-widgets.md)
