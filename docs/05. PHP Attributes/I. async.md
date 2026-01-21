# #[Async]

Atribut `#[Async]` menandai method untuk eksekusi paralel, memungkinkan beberapa aksi berjalan secara bersamaan tanpa menunggu satu sama lain.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Kapan Tidak Menggunakan](#kapan-tidak-menggunakan)
- [Pendekatan Alternatif](#pendekatan-alternatif)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Async;
use Livewire\Component;

class Dashboard extends Component
{
    #[Async]
    public function refreshStats()
    {
        // Operasi yang memakan waktu...
    }

    #[Async]
    public function refreshChart()
    {
        // Operasi lain yang memakan waktu...
    }
}
```

```html
<button wire:click="refreshStats">Refresh Stats</button>
<button wire:click="refreshChart">Refresh Chart</button>
```

Kedua method dapat dipanggil bersamaan dan akan berjalan paralel.

---

## Kapan Menggunakan

- Operasi independen yang tidak bergantung satu sama lain
- Aksi yang membutuhkan waktu lama
- Polling multiple data sources

---

## Kapan Tidak Menggunakan

- Aksi yang memiliki efek samping pada state yang sama
- Operasi yang harus dijalankan secara berurutan
- Aksi yang bergantung pada hasil aksi lain

---

## Pendekatan Alternatif

### Menggunakan Modifier .async

```html
<button wire:click.async="refreshStats">Refresh Stats</button>
```
