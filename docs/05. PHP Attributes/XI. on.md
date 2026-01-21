# #[On]

Atribut `#[On]` menandai method sebagai listener untuk event yang didispatch dari komponen lain atau JavaScript.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Dispatching Events](#dispatching-events)
- [Passing Data](#passing-data)
- [Dynamic Event Names](#dynamic-event-names)
- [Multiple Listeners](#multiple-listeners)
- [Browser Events](#browser-events)
- [Kapan Menggunakan](#kapan-menggunakan)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\On;
use Livewire\Component;

class NotificationList extends Component
{
    public $notifications = [];

    #[On('notification-created')]
    public function addNotification($message)
    {
        $this->notifications[] = $message;
    }
}
```

---

## Dispatching Events

```php
// Dari komponen lain
$this->dispatch('notification-created', message: 'Hello!');
```

Atau dari template:

```html
<button wire:click="$dispatch('notification-created', { message: 'Hello!' })">
  Notify
</button>
```

---

## Passing Data

```php
#[On('user-updated')]
public function handleUserUpdate($userId, $name)
{
    // ...
}
```

```php
$this->dispatch('user-updated', userId: 1, name: 'John');
```

---

## Dynamic Event Names

```php
#[On('post-updated.{postId}')]
public function handlePostUpdate()
{
    // Listener untuk event spesifik ke post ini
}
```

---

## Multiple Listeners

```php
#[On('created')]
#[On('updated')]
public function refresh()
{
    // Dipanggil untuk kedua event
}
```

---

## Browser Events

```php
#[On('native:scroll')]
public function handleScroll()
{
    // ...
}
```

---

## Kapan Menggunakan

- Komunikasi antar komponen
- Event dari JavaScript
- Event-driven architecture
