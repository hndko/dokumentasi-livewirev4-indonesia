# #[Modelable]

Atribut `#[Modelable]` memungkinkan properti digunakan dengan `wire:model` dari komponen parent, membuat komponen child yang dapat di-bind seperti input HTML native.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Cara Kerjanya](#cara-kerjanya)
- [Membuat Komponen Input Reusable](#membuat-komponen-input-reusable)
- [Modifiers](#modifiers)
- [Kapan Menggunakan](#kapan-menggunakan)

---

## Penggunaan Dasar

**Komponen Child (ColorPicker):**

```php
use Livewire\Attributes\Modelable;
use Livewire\Component;

class ColorPicker extends Component
{
    #[Modelable]
    public $value = '#000000';
}
```

```html
<div>
  <input type="color" wire:model="value" />
</div>
```

**Komponen Parent:**

```php
class Form extends Component
{
    public $color = '#ff0000';
}
```

```html
<livewire:color-picker wire:model="color" />
```

---

## Cara Kerjanya

Saat properti `#[Modelable]` berubah di child, perubahan otomatis disinkronkan ke parent.

---

## Membuat Komponen Input Reusable

```php
class DatePicker extends Component
{
    #[Modelable]
    public $value;

    public function render()
    {
        return view('livewire.date-picker');
    }
}
```

```html
<!-- livewire/date-picker.blade.php -->
<div x-data="{ open: false }">
  <input type="text" wire:model="value" x-on:focus="open = true" />

  <div x-show="open" x-on:click.away="open = false">
    <!-- Calendar UI -->
  </div>
</div>
```

---

## Modifiers

Modifiers dari parent diteruskan ke child:

```html
<livewire:date-picker wire:model.live="date" />
```

---

## Kapan Menggunakan

- Custom input components
- Complex form controls
- Wrapper untuk library pihak ketiga
