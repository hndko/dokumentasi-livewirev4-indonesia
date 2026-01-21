# Loading States

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Loading states memungkinkan Anda menampilkan indikator visual saat Livewire sedang memproses request. Ini meningkatkan pengalaman pengguna dengan memberikan feedback bahwa sesuatu sedang terjadi.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Cara Kerjanya](#cara-kerjanya)
- [Styling dengan Tailwind](#styling-dengan-tailwind)
- [Keuntungan dibanding wire:loading](#keuntungan-dibanding-wireloading)
- [Menggunakan dengan Plain CSS](#menggunakan-dengan-plain-css)

---

## Penggunaan Dasar

Livewire otomatis menambahkan atribut `data-loading` ke elemen form saat request sedang diproses.

```html
<form wire:submit="save">
  <input wire:model="title" type="text" />
  <button type="submit">Save</button>
</form>
```

---

## Cara Kerjanya

Saat form dikirim:

1. Livewire menambahkan atribut `data-loading` ke elemen form
2. Button dan input di dalam form otomatis dinonaktifkan
3. Setelah request selesai, atribut dihapus dan elemen diaktifkan kembali

---

## Styling dengan Tailwind

### Basic Styling

```html
<button type="submit" class="data-loading:opacity-50">Save</button>
```

### Menampilkan Elemen Selama Loading

```html
<button type="submit">
  <span class="in-data-loading:hidden">Save</span>
  <span class="not-in-data-loading:hidden">
    <svg>...</svg>
    <!-- Loading spinner -->
  </span>
</button>
```

### Styling Children

```html
<form wire:submit="save" class="group">
  <button type="submit" class="group-data-loading:opacity-50">Save</button>
</form>
```

### Styling Parents

```html
<div class="has-data-loading:bg-gray-100">
  <form wire:submit="save">
    <!-- ... -->
  </form>
</div>
```

### Styling Siblings

```html
<form wire:submit="save">
  <button type="submit">Save</button>
</form>
<div class="peer-data-loading:opacity-50">
  <!-- Konten sibling -->
</div>
```

---

## Keuntungan dibanding wire:loading

Menggunakan selector `data-loading` sering lebih sederhana dan lebih fleksibel daripada `wire:loading`:

```html
<!-- Dengan wire:loading -->
<button type="submit">
  <span>Save</span>
  <span wire:loading>Loading...</span>
</button>

<!-- Dengan data-loading (lebih bersih) -->
<button type="submit">
  <span class="in-data-loading:hidden">Save</span>
  <span class="not-in-data-loading:hidden">Loading...</span>
</button>
```

---

## Menggunakan dengan Plain CSS

```css
/* Sembunyikan elemen saat loading */
[data-loading] .hide-on-loading {
  display: none;
}

/* Tampilkan elemen saat loading */
.show-on-loading {
  display: none;
}
[data-loading] .show-on-loading {
  display: block;
}

/* Kurangi opacity saat loading */
[data-loading] button {
  opacity: 0.5;
  pointer-events: none;
}
```

### Contoh Lengkap

```html
<form wire:submit="save">
  <input wire:model="title" type="text" />

  <button type="submit">
    <span class="hide-on-loading">Save</span>
    <span class="show-on-loading">
      <svg class="animate-spin">...</svg>
      Saving...
    </span>
  </button>
</form>

<style>
  [data-loading] .hide-on-loading {
    display: none;
  }

  .show-on-loading {
    display: none;
  }

  [data-loading] .show-on-loading {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
  }
</style>
```
