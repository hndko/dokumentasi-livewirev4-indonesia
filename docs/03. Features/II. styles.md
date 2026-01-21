# Styles

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda menyertakan style khusus komponen langsung di komponen single-file dan multi-file Anda. Style ini otomatis di-scope ke komponen Anda, mencegahnya bocor ke bagian lain aplikasi.

---

## Daftar Isi

- [Scoped Styles](#scoped-styles)
  - [Komponen Single-file](#komponen-single-file)
  - [Komponen Multi-file](#komponen-multi-file)
- [Cara Scoping Bekerja](#cara-scoping-bekerja)
  - [Menargetkan Root Komponen](#menargetkan-root-komponen)
- [Global Styles](#global-styles)
- [Menggabungkan Scoped dan Global Styles](#menggabungkan-scoped-dan-global-styles)
- [Deduplikasi Style](#deduplikasi-style)
- [Kapan Menggunakan Component Styles](#kapan-menggunakan-component-styles)
- [Dukungan Browser](#dukungan-browser)

---

## Scoped Styles

Secara default, style yang didefinisikan di komponen Anda hanya di-scope ke komponen tersebut. Ini berarti selector CSS Anda hanya akan mempengaruhi elemen dalam komponen Anda, bahkan jika selector yang sama ada di tempat lain di halaman.

### Komponen Single-file

Tambahkan tag `<style>` di level root komponen single-file Anda:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $count = 0;

    public function increment()
    {
        $this->count++;
    }
};
?>

<div>
    <h1 class="title">Count: {{ $count }}</h1>
    <button class="btn" wire:click="increment">+</button>
</div>

<style>
.title {
    color: blue;
    font-size: 2rem;
}

.btn {
    background: indigo;
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
}
</style>
```

Style `.title` dan `.btn` hanya akan diterapkan ke elemen dalam komponen ini.

### Komponen Multi-file

Buat file CSS dengan nama yang sama dengan komponen Anda:

```
resources/views/components/counter/
├── counter.php
├── counter.blade.php
└── counter.css  # Scoped styles
```

**counter.css**

```css
.title {
  color: blue;
  font-size: 2rem;
}

.btn {
  background: indigo;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
}
```

---

## Cara Scoping Bekerja

Livewire otomatis membungkus style Anda dalam selector yang menargetkan elemen root komponen Anda. CSS Anda ditransformasi menggunakan CSS nesting:

```css
/* Yang Anda tulis */
.btn {
  background: blue;
}

/* Yang disajikan */
[wire\:name="counter"] {
  .btn {
    background: blue;
  }
}
```

### Menargetkan Root Komponen

Anda dapat menggunakan selector `&` untuk menargetkan elemen root komponen:

```html
<style>
  & {
    border: 2px solid gray;
    padding: 1rem;
  }

  .title {
    margin-top: 0;
  }
</style>
```

---

## Global Styles

Terkadang Anda membutuhkan style yang berlaku secara global. Tambahkan atribut `global` ke tag style:

### Komponen Single-file

```html
<style global>
  body {
    font-family: system-ui, sans-serif;
  }

  .prose {
    max-width: 65ch;
    line-height: 1.6;
  }
</style>
```

### Komponen Multi-file

Buat file dengan ekstensi `.global.css`:

```
resources/views/components/counter/
├── counter.php
├── counter.blade.php
├── counter.css        # Scoped styles
└── counter.global.css # Global styles
```

---

## Menggabungkan Scoped dan Global Styles

Anda dapat menggunakan scoped dan global styles dalam komponen yang sama:

```php
<?php

use Livewire\Component;

new class extends Component {
    // ...
};
?>

<div class="counter">
    <h1 class="title">My Counter</h1>
</div>

<style>
.title {
    color: blue;
}
</style>

<style global>
.counter-page-layout {
    display: grid;
    place-items: center;
}
</style>
```

---

## Deduplikasi Style

Livewire otomatis mendeduplikasi style saat beberapa instance komponen yang sama muncul di halaman. Style hanya dimuat sekali.

---

## Kapan Menggunakan Component Styles

**Gunakan scoped styles saat:**

- Styling spesifik untuk satu komponen
- Anda ingin menghindari tabrakan nama class CSS
- Anda membangun komponen yang dapat digunakan kembali dan mandiri

**Gunakan global styles saat:**

- Anda perlu style elemen di luar komponen Anda
- Anda mendefinisikan class utilitas yang digunakan di beberapa komponen
- Anda meng-override style library pihak ketiga

**Gunakan @assets untuk stylesheet eksternal:**

```html
@assets
<link rel="stylesheet" href="https://cdn.example.com/library.css" />
@endassets
```

---

## Dukungan Browser

Scoped styles menggunakan [CSS nesting](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting), yang didukung di semua browser modern (Chrome 120+, Firefox 117+, Safari 17.2+). Untuk dukungan browser lama, pertimbangkan menggunakan CSS preprocessor atau direktif `@assets` dengan stylesheet yang sudah dikompilasi.
