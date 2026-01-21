# Step 5: Draggable Widgets

## 🎯 Tujuan

- Implementasi drag-and-drop dengan wire:sort
- Persist order dengan #[Session]

---

## 📝 Langkah 5.1: Generate Widget Grid

```bash
php artisan make:livewire Widgets/WidgetGrid
```

---

## 📝 Langkah 5.2: WidgetGrid Component

```php
<?php
// app/Livewire/Widgets/WidgetGrid.php

namespace App\Livewire\Widgets;

use Livewire\Attributes\Session;
use Livewire\Component;

class WidgetGrid extends Component
{
    #[Session]
    public array $widgets = [
        ['id' => 1, 'title' => 'Recent Orders'],
        ['id' => 2, 'title' => 'Top Products'],
        ['id' => 3, 'title' => 'Activity Log'],
    ];

    public function updateOrder($items)
    {
        // Reorder berdasarkan drag result
        $this->widgets = collect($items)
            ->map(fn($item) => collect($this->widgets)
                ->firstWhere('id', $item['value']))
            ->toArray();
    }

    public function render()
    {
        return view('livewire.widgets.widget-grid');
    }
}
```

---

## 📝 Langkah 5.3: WidgetGrid View

```html
<div>
  <h3 class="font-bold mb-4">🧩 Widgets (Drag to reorder)</h3>

  <div wire:sort="updateOrder" class="grid md:grid-cols-3 gap-4">
    @foreach ($widgets as $widget)
    <div
      wire:key="widget-{{ $widget['id'] }}"
      wire:sort.item="{{ $widget['id'] }}"
      class="bg-white p-4 rounded-lg shadow cursor-move"
    >
      <div class="flex items-center gap-2 mb-3">
        <span wire:sort.handle class="cursor-grab">⋮⋮</span>
        <h4 class="font-medium">{{ $widget['title'] }}</h4>
      </div>
      <p class="text-gray-500 text-sm">Widget content here</p>
    </div>
    @endforeach
  </div>
</div>
```

---

## 📝 Penjelasan

| Directive          | Deskripsi                |
| ------------------ | ------------------------ |
| `wire:sort`        | Container sortable       |
| `wire:sort.item`   | Item yang bisa digeser   |
| `wire:sort.handle` | Handle untuk drag        |
| `#[Session]`       | Persist order di session |

---

## ✅ Checkpoint

- [x] Draggable widgets
- [x] Order persisted di session

---

## ⬅️ [Step 4](04-charts-javascript.md) | ➡️ [Step 6: Streaming](06-streaming-ai.md)
