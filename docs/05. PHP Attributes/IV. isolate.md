# #[Isolate]

Atribut `#[Isolate]` mencegah komponen dibundel dengan komponen lain dalam satu request, membuatnya diproses secara terpisah.

---

## Daftar Isi

- [Mengapa Bundling Penting](#mengapa-bundling-penting)
- [Penggunaan Dasar](#penggunaan-dasar)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Contoh: Polling Components](#contoh-polling-components)
- [Trade-offs](#trade-offs)

---

## Mengapa Bundling Penting

Secara default, Livewire mengelompokkan multiple request komponen menjadi satu untuk efisiensi. Namun, ada kasus di mana Anda ingin komponen diproses secara terpisah.

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Isolate;
use Livewire\Component;

#[Isolate]
class LiveClock extends Component
{
    public function render()
    {
        return view('livewire.live-clock', [
            'time' => now()->format('H:i:s'),
        ]);
    }
}
```

---

## Kapan Menggunakan

- Komponen dengan polling yang tidak boleh mempengaruhi komponen lain
- Komponen real-time yang harus diupdate secara independen
- Komponen yang memiliki operasi lama yang tidak boleh memblokir yang lain

---

## Contoh: Polling Components

```php
#[Isolate]
class StockTicker extends Component
{
    public function render()
    {
        return view('livewire.stock-ticker', [
            'price' => Stock::latest()->value('price'),
        ]);
    }
}
```

```html
<div wire:poll.3s>Current price: {{ $price }}</div>
```

---

## Trade-offs

**Keuntungan:**

- Komponen tidak memblokir satu sama lain
- Response time lebih dapat diprediksi

**Kerugian:**

- Lebih banyak network requests
- Overhead tambahan per request

> **Catatan:** Komponen `#[Lazy]` dan `#[Defer]` otomatis terisolasi.
