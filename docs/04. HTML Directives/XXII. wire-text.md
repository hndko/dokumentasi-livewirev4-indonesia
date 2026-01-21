# wire:text

Direktif `wire:text` mengikat konten teks elemen ke properti komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<span wire:text="$title"></span>
```

Setara dengan `{{ $title }}` tetapi diperbarui secara reaktif di klien.

```html
<div>
  <input wire:model="title" />
  <span wire:text="$title"></span>
</div>
```

Saat pengguna mengetik, span akan diperbarui secara instan tanpa network request.

---

## Reference

### Sintaks

```
wire:text="$property"
wire:text="expression"
```
