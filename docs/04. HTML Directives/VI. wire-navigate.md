# wire:navigate

Direktif `wire:navigate` menyediakan navigasi SPA-style untuk link di aplikasi Anda, menghindari reload halaman penuh.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Styling Active Links](#styling-active-links)
- [Prefetching on Hover](#prefetching-on-hover)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<nav>
  <a href="/" wire:navigate>Dashboard</a>
  <a href="/posts" wire:navigate>Posts</a>
  <a href="/users" wire:navigate>Users</a>
</nav>
```

Saat link diklik:

1. Livewire mencegah browser mengunjungi halaman baru
2. Livewire meminta halaman di background
3. Livewire mengganti konten dengan halaman baru

Teknik ini menghasilkan waktu load halaman yang jauh lebih cepat.

---

## Styling Active Links

Livewire otomatis menambahkan atribut `data-current` ke link yang cocok dengan halaman saat ini:

```html
<a href="/posts" wire:navigate class="data-current:font-bold"> Posts </a>
```

Atau gunakan direktif `wire:current`:

```html
<a href="/posts" wire:navigate wire:current="font-bold text-blue-600">
  Posts
</a>
```

---

## Prefetching on Hover

Untuk navigasi yang lebih cepat, prefetch halaman saat pengguna hover:

```html
<a href="/posts" wire:navigate.hover>Posts</a>
```

---

## Reference

### Modifiers

| Modifier | Deskripsi           |
| -------- | ------------------- |
| `.hover` | Prefetch saat hover |
