# Lazy Loading

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda untuk lazy load komponen yang tidak segera terlihat di halaman. Ini dapat secara drastis meningkatkan performa load halaman awal dengan menunda rendering komponen tertentu sampai diperlukan.

---

## Daftar Isi

- [Lazy vs Defer](#lazy-vs-defer)
- [Contoh Dasar](#contoh-dasar)
- [Merender Placeholder HTML](#merender-placeholder-html)
- [Loading Segera Setelah Page Load](#loading-segera-setelah-page-load)
- [Passing Props](#passing-props)
- [Enforce Lazy atau Defer Secara Default](#enforce-lazy-atau-defer-secara-default)
- [Bundling Multiple Lazy Components](#bundling-multiple-lazy-components)
- [Full-page Lazy Loading](#full-page-lazy-loading)
- [Default Placeholder View](#default-placeholder-view)
- [Menonaktifkan Lazy Loading untuk Tests](#menonaktifkan-lazy-loading-untuk-tests)

---

## Lazy vs Defer

- **Lazy**: Komponen dimuat saat masuk viewport (di-scroll ke view)
- **Defer**: Komponen dimuat segera setelah page load (terlepas dari visibilitas)

---

## Contoh Dasar

Tambahkan atribut `#[Lazy]` ke komponen Anda:

```php
<?php

use Livewire\Attributes\Lazy;
use Livewire\Component;

#[Lazy]
new class extends Component {
    // ...
};
```

Atau gunakan atribut `lazy` saat merender komponen:

```html
<livewire:dashboard lazy />
```

---

## Merender Placeholder HTML

### Menggunakan Direktif @placeholder

```html
<div>
  @placeholder
  <div class="animate-pulse bg-gray-200 h-32 rounded"></div>
  @endplaceholder

  <!-- Konten komponen sebenarnya -->
  <div>{{ $content }}</div>
</div>
```

### Menggunakan Method placeholder()

```php
<?php

use Livewire\Attributes\Lazy;
use Livewire\Component;

#[Lazy]
new class extends Component {
    public static function placeholder()
    {
        return <<<'HTML'
        <div class="animate-pulse bg-gray-200 h-32 rounded"></div>
        HTML;
    }

    // ...
};
```

---

## Loading Segera Setelah Page Load

Gunakan `#[Defer]` untuk memuat komponen segera setelah halaman dimuat:

```php
<?php

use Livewire\Attributes\Defer;
use Livewire\Component;

#[Defer]
new class extends Component {
    // ...
};
```

Atau sebagai atribut:

```html
<livewire:dashboard defer />
```

---

## Passing Props

Props dapat di-pass ke komponen lazy seperti biasa:

```html
<livewire:user-profile :user="$user" lazy />
```

---

## Enforce Lazy atau Defer Secara Default

Untuk memaksa komponen selalu lazy atau defer:

```php
<?php

use Livewire\Attributes\Lazy;
use Livewire\Component;

#[Lazy(enforce: true)]
new class extends Component {
    // ...
};
```

---

## Bundling Multiple Lazy Components

Untuk mengurangi jumlah request, Anda dapat mengelompokkan beberapa komponen lazy:

### Menggunakan Parameter bundle

```html
<livewire:stats lazy bundle="dashboard" />
<livewire:chart lazy bundle="dashboard" />
<livewire:table lazy bundle="dashboard" />
```

### Menggunakan Modifier .bundle

```html
<livewire:stats lazy.bundle /> <livewire:chart lazy.bundle />
```

---

## Full-page Lazy Loading

### Lazy Loading Halaman Penuh

```php
<?php

use Livewire\Attributes\Lazy;
use Livewire\Component;

#[Lazy]
new class extends Component {
    // ...
};
```

### Deferring Halaman Penuh

```php
<?php

use Livewire\Attributes\Defer;
use Livewire\Component;

#[Defer]
new class extends Component {
    // ...
};
```

### Menonaktifkan lazy/defer Loading

```html
<livewire:dashboard :lazy="false" />
```

---

## Default Placeholder View

Buat view placeholder default di `resources/views/vendor/livewire/placeholder.blade.php`:

```html
<div class="animate-pulse bg-gray-200 h-32 rounded"></div>
```

---

## Menonaktifkan Lazy Loading untuk Tests

```php
use Livewire\Livewire;

Livewire::withoutLazyLoading(function () {
    // Tests di sini
});
```
