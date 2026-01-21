# #[Defer]

Atribut `#[Defer]` menunda loading komponen hingga setelah halaman dimuat, terlepas dari apakah komponen terlihat di viewport.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Lazy vs Defer](#lazy-vs-defer)
- [Rendering Placeholders](#rendering-placeholders)
- [Bundling Requests](#bundling-requests)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Defer;
use Livewire\Component;

#[Defer]
class ExpensiveComponent extends Component
{
    public function render()
    {
        return view('livewire.expensive-component');
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
#[Defer]
class ExpensiveComponent extends Component
{
    public static function placeholder()
    {
        return <<<'HTML'
        <div class="animate-pulse bg-gray-200 h-32 rounded"></div>
        HTML;
    }
}
```

Atau menggunakan direktif `@placeholder`:

```html
<div>
  @placeholder
  <div class="animate-pulse bg-gray-200 h-32 rounded"></div>
  @endplaceholder

  <!-- Konten sebenarnya -->
</div>
```

---

## Bundling Requests

```html
<livewire:component-a defer bundle="dashboard" />
<livewire:component-b defer bundle="dashboard" />
```

---

## Kapan Menggunakan

- Konten yang tidak kritis untuk render awal
- Komponen di bawah fold
- Operasi database yang mahal

---

## Reference

| Parameter | Deskripsi                        |
| --------- | -------------------------------- |
| `enforce` | Paksa defer bahkan jika override |
