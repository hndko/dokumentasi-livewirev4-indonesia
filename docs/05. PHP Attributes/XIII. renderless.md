# #[Renderless]

Atribut `#[Renderless]` mencegah komponen dirender ulang setelah method dieksekusi.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Pendekatan Alternatif](#pendekatan-alternatif)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Renderless;
use Livewire\Component;

class Analytics extends Component
{
    #[Renderless]
    public function trackClick($buttonId)
    {
        AnalyticsService::track('button_click', ['id' => $buttonId]);
    }
}
```

```html
<button wire:click="trackClick('signup')">Sign Up</button>
```

Method `trackClick` dieksekusi tanpa merender ulang komponen.

---

## Kapan Menggunakan

- Tracking/analytics
- Logging
- Aksi yang tidak mengubah UI
- Side effects tanpa perubahan state yang terlihat

---

## Pendekatan Alternatif

### Menggunakan skipRender()

```php
public function trackClick($buttonId)
{
    AnalyticsService::track('button_click', ['id' => $buttonId]);

    $this->skipRender();
}
```

### Menggunakan Modifier .renderless

```html
<button wire:click.renderless="trackClick('signup')">Sign Up</button>
```
