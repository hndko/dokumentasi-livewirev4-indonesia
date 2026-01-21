# #[Js]

Atribut `#[Js]` menandai method untuk dieksekusi sepenuhnya di client-side menggunakan JavaScript, tanpa network request ke server.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Mengakses $wire](#mengakses-wire)
- [Kapan Menggunakan](#kapan-menggunakan)
- [JavaScript Actions vs #[Js]](#javascript-actions-vs-js)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Js;
use Livewire\Component;

class Counter extends Component
{
    public $count = 0;

    #[Js]
    public function increment()
    {
        return '$wire.count++';
    }
}
```

```html
<div>
  <span>{{ $count }}</span>
  <button wire:click="increment">+</button>
</div>
```

Saat button diklik, increment terjadi di client tanpa server roundtrip.

---

## Mengakses $wire

Di dalam method `#[Js]`, Anda dapat menggunakan `$wire` untuk mengakses state dan method komponen:

```php
#[Js]
public function toggle()
{
    return <<<'JS'
        $wire.showModal = !$wire.showModal;
    JS;
}
```

---

## Kapan Menggunakan

- Operasi yang tidak memerlukan server (toggle, increment)
- Meningkatkan responsivitas UI
- Mengurangi network requests

---

## JavaScript Actions vs #[Js]

| Pendekatan | Deskripsi                               |
| ---------- | --------------------------------------- |
| `#[Js]`    | Method PHP yang menghasilkan JavaScript |
| JS Actions | JavaScript inline di template           |

```html
<!-- JavaScript action di template -->
<button wire:click="$set('count', count + 1)">+</button>

<!-- Menggunakan #[Js] method -->
<button wire:click="increment">+</button>
```
