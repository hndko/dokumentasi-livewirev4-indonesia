# #[Lazy]

Atribut `#[Lazy]` menunda loading komponen hingga komponen terlihat di viewport (di-scroll ke view).

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Lazy vs Defer](#lazy-vs-defer)
- [Rendering Placeholders](#rendering-placeholders)
- [Bundling Requests](#bundling-requests)
- [Override Atribut](#override-atribut)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Lazy;
use Livewire\Component;

#[Lazy]
class HeavyComponent extends Component
{
    public function render()
    {
        return view('livewire.heavy-component');
    }
}
```

---

## Lazy vs Defer

| Atribut    | Perilaku                          |
| ---------- | --------------------------------- |
| `#[Lazy]`  | Dimuat saat di-scroll ke viewport |
| `#[Defer]` | Dimuat segera setelah page load   |

---

## Rendering Placeholders

```php
#[Lazy]
class HeavyComponent extends Component
{
    public static function placeholder()
    {
        return <<<'HTML'
        <div class="animate-pulse bg-gray-200 h-32 rounded"></div>
        HTML;
    }
}
```

---

## Bundling Requests

```html
<livewire:component-a lazy bundle="below-fold" />
<livewire:component-b lazy bundle="below-fold" />
```

---

## Override Atribut

```html
<!-- Menonaktifkan lazy -->
<livewire:heavy-component :lazy="false" />
```

---

## Kapan Menggunakan

- Komponen di bawah fold
- Komponen yang memerlukan operasi mahal
- Infinite scroll content

---

## Reference

| Parameter | Deskripsi                       |
| --------- | ------------------------------- |
| `enforce` | Paksa lazy bahkan jika override |
