# wire:poll

Direktif `wire:poll` merefresh komponen pada interval tertentu.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Timing Control](#timing-control)
- [Background Throttling](#background-throttling)
- [Viewport Throttling](#viewport-throttling)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:poll>Current time: {{ now() }}</div>
```

Secara default, komponen direfresh setiap 2 detik.

---

## Timing Control

Sesuaikan interval polling:

```html
<!-- Setiap 5 detik -->
<div wire:poll.5s>...</div>

<!-- Setiap 500 milidetik -->
<div wire:poll.500ms>...</div>
```

Panggil method tertentu:

```html
<div wire:poll="refreshData">...</div>
```

---

## Background Throttling

Secara default, Livewire memperlambat polling saat tab browser di background. Gunakan `.keep-alive` untuk mempertahankan interval:

```html
<div wire:poll.keep-alive>...</div>
```

---

## Viewport Throttling

Gunakan `.visible` untuk hanya poll saat elemen terlihat:

```html
<div wire:poll.visible>...</div>
```

---

## Reference

### Modifiers

| Modifier      | Deskripsi                     |
| ------------- | ----------------------------- |
| `.Xs`         | Interval dalam detik          |
| `.Xms`        | Interval dalam milidetik      |
| `.keep-alive` | Jangan throttle di background |
| `.visible`    | Hanya poll saat terlihat      |
