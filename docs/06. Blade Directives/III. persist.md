# @persist

Direktif `@persist` mempertahankan elemen DOM saat navigasi antar halaman menggunakan `wire:navigate`.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kasus Penggunaan Umum](#kasus-penggunaan-umum)
- [Penempatan di Layouts](#penempatan-di-layouts)
- [Mempertahankan Posisi Scroll](#mempertahankan-posisi-scroll)
- [Cara Kerjanya](#cara-kerjanya)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
@persist('player')
<audio src="{{ $episode->file }}" controls></audio>
@endpersist
```

Audio player akan tetap berputar saat pengguna menavigasi antar halaman.

---

## Kasus Penggunaan Umum

### Audio/Video Player

```html
@persist('music-player')
<div class="fixed bottom-0 w-full">
  <audio src="{{ $currentTrack }}" controls></audio>
</div>
@endpersist
```

### Chat Widget

```html
@persist('chat')
<div class="fixed bottom-4 right-4">
  <livewire:chat-widget />
</div>
@endpersist
```

### Navigation Sidebar

```html
@persist('sidebar')
<nav class="sidebar">
  <!-- Menu items -->
</nav>
@endpersist
```

---

## Penempatan di Layouts

Elemen yang dipertahankan harus ditempatkan di layout, bukan di komponen:

```html
<!-- layouts/app.blade.php -->
<!DOCTYPE html>
<html>
  <body>
    @persist('player')
    <audio id="player" controls></audio>
    @endpersist {{ $slot }}
  </body>
</html>
```

---

## Mempertahankan Posisi Scroll

```html
@persist('sidebar')
<div class="overflow-y-auto" wire:navigate:scroll>
  <!-- Scrollable content -->
</div>
@endpersist
```

---

## Cara Kerjanya

1. Saat navigasi terjadi, Livewire menyimpan elemen dengan `@persist`
2. Halaman baru dirender
3. Elemen yang dipertahankan dikembalikan ke posisinya

---

## Reference

| Parameter | Deskripsi                      |
| --------- | ------------------------------ |
| `name`    | Unique identifier untuk elemen |
