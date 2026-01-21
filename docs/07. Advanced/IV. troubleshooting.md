# Troubleshooting

Panduan untuk mendiagnosis dan memperbaiki masalah umum di Livewire.

---

## Daftar Isi

- [Component Mismatches](#component-mismatches)
- [Multiple Instances of Alpine](#multiple-instances-of-alpine)
- [Missing @alpinejs/ui](#missing-alpinejs-ui)

---

## Component Mismatches

Salah satu masalah paling umum adalah "component mismatch" - ketika Livewire mengacaukan komponen yang satu dengan yang lain.

### Gejala

- Komponen menampilkan data yang salah
- State tercampur antar komponen
- Error: "Component mismatch"

### Penyebab

Biasanya terjadi saat merender komponen dalam loop tanpa key unik.

### Solusi: Menambahkan wire:key

```html
@foreach ($users as $user)
<livewire:user-card :user="$user" :key="$user->id" />
@endforeach
```

Atau untuk elemen HTML biasa:

```html
@foreach ($items as $item)
<div wire:key="{{ $item->id }}">{{ $item->name }}</div>
@endforeach
```

---

## Multiple Instances of Alpine

Livewire menyertakan Alpine.js secara otomatis. Jika ada Alpine lain di halaman, akan terjadi konflik.

### Gejala

- x-data tidak bekerja
- Console error tentang Alpine
- Komponen Livewire tidak responsif

### Solusi: Removing Laravel Breeze's Alpine

Jika menggunakan Laravel Breeze:

```js
// resources/js/app.js
// Hapus baris ini:
// import Alpine from 'alpinejs';
// Alpine.start();
```

### Solusi: Removing CDN Version

Hapus script Alpine dari layout:

```html
<!-- Hapus ini -->
<script src="https://unpkg.com/alpinejs"></script>
```

---

## Missing @alpinejs/ui

Jika menggunakan komponen Alpine UI:

### Gejala

- Error tentang komponen UI tidak ditemukan
- x-dialog, x-popover tidak bekerja

### Solusi

```bash
npm install @alpinejs/ui
```

```js
// resources/js/app.js
import ui from "@alpinejs/ui";
Alpine.plugin(ui);
```
