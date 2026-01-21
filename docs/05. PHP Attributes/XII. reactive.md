# #[Reactive]

Atribut `#[Reactive]` membuat props dari parent secara otomatis disinkronkan ke child saat parent dirender ulang.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Mengapa Props Tidak Reaktif Secara Default](#mengapa-props-tidak-reaktif-secara-default)
- [Cara Kerjanya](#cara-kerjanya)
- [Pertimbangan Performa](#pertimbangan-performa)
- [Alternatif: Events](#alternatif-events)

---

## Penggunaan Dasar

**Parent Component:**

```php
class SearchPage extends Component
{
    public $query = '';
}
```

```html
<div>
  <input wire:model.live="query" />
  <livewire:search-results :query="$query" />
</div>
```

**Child Component:**

```php
use Livewire\Attributes\Reactive;
use Livewire\Component;

class SearchResults extends Component
{
    #[Reactive]
    public $query;

    public function render()
    {
        return view('livewire.search-results', [
            'results' => Post::where('title', 'like', "%{$this->query}%")->get(),
        ]);
    }
}
```

---

## Mengapa Props Tidak Reaktif Secara Default

Secara default, props hanya di-pass saat child pertama kali dirender. Ini untuk alasan performa - tidak perlu merender ulang semua child setiap kali parent berubah.

---

## Cara Kerjanya

Saat parent dirender ulang dan props berubah, child dengan `#[Reactive]` juga akan dirender ulang dengan nilai baru.

---

## Pertimbangan Performa

Gunakan `#[Reactive]` dengan bijak - setiap child dengan reactive props akan dirender ulang bersama parent.

---

## Alternatif: Events

```php
// Parent
$this->dispatch('query-updated', query: $this->query);

// Child
#[On('query-updated')]
public function updateQuery($query)
{
    $this->query = $query;
}
```
