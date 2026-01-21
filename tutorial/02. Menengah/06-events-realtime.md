# Step 6: Events & Real-time Updates

## 🎯 Tujuan

- Dispatch events antar komponen
- Membuat notification komponen

---

## 📝 Langkah 6.1: Generate Notification Component

```bash
php artisan make:livewire Notifications
```

---

## 📝 Langkah 6.2: Edit Notifications Component

```php
<?php
// app/Livewire/Notifications.php

namespace App\Livewire;

use Livewire\Attributes\On;
use Livewire\Component;

class Notifications extends Component
{
    public array $notifications = [];

    #[On('comment-added')]
    public function onCommentAdded()
    {
        $this->add('Komentar berhasil dikirim!', 'success');
    }

    #[On('post-created')]
    public function onPostCreated()
    {
        $this->add('Artikel berhasil dipublikasikan!', 'success');
    }

    protected function add(string $message, string $type)
    {
        $id = uniqid();
        $this->notifications[] = compact('id', 'message', 'type');
        $this->dispatch('remove-notification', id: $id)->self()->delay(5000);
    }

    #[On('remove-notification')]
    public function remove(string $id)
    {
        $this->notifications = array_filter($this->notifications, fn($n) => $n['id'] !== $id);
    }

    public function render()
    {
        return view('livewire.notifications');
    }
}
```

---

## 📝 Langkah 6.3: Edit Notifications View

```html
<!-- resources/views/livewire/notifications.blade.php -->
<div class="fixed top-4 right-4 z-50 space-y-2 w-80">
  @foreach ($notifications as $n)
  <div
    wire:key="notif-{{ $n['id'] }}"
    class="p-4 rounded-lg shadow-lg {{ $n['type'] === 'success' ? 'bg-green-500' : 'bg-red-500' }} text-white"
  >
    <div class="flex justify-between">
      <p>{{ $n['message'] }}</p>
      <button wire:click="remove('{{ $n['id'] }}')">✕</button>
    </div>
  </div>
  @endforeach
</div>
```

---

## 📝 Langkah 6.4: Tambahkan ke Layout

```html
<body>
  <livewire:notifications />
  <!-- ... rest of layout ... -->
</body>
```

---

## ✅ Checkpoint

- [x] Notification component dengan events
- [x] Auto-dismiss notifications

---

## ⬅️ [Step 5](05-show-post-comments.md) | ➡️ [Step 7](07-spa-lazy-loading.md)
