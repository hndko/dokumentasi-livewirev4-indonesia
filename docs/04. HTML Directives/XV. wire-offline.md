# wire:offline

Direktif `wire:offline` menampilkan atau menyembunyikan elemen saat pengguna kehilangan koneksi internet.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Toggle Classes](#toggle-classes)
- [Toggle Attributes](#toggle-attributes)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:offline>Anda sedang offline.</div>
```

Pesan akan muncul saat pengguna kehilangan koneksi.

---

## Toggle Classes

```html
<div wire:offline.class="bg-red-100">Konten</div>
```

---

## Toggle Attributes

```html
<button wire:offline.attr="disabled">Submit</button>
```

---

## Reference

### Modifiers

| Modifier        | Deskripsi                     |
| --------------- | ----------------------------- |
| `.class`        | Toggle class saat offline     |
| `.class.remove` | Hapus class saat offline      |
| `.attr`         | Toggle attribute saat offline |
