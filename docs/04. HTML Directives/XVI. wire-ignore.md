# wire:ignore

Direktif `wire:ignore` mencegah Livewire memperbarui bagian tertentu dari DOM saat render ulang.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Self-only](#self-only)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div>
  <div wire:ignore>
    <!-- Konten ini tidak akan diperbarui oleh Livewire -->
    <input type="text" id="editor" />
  </div>
</div>
```

Berguna untuk integrasi dengan library JavaScript pihak ketiga yang memanipulasi DOM.

---

## Self-only

Gunakan `.self` untuk hanya mengabaikan elemen itu sendiri, bukan children-nya:

```html
<div wire:ignore.self>
  <!-- Elemen ini diabaikan, tapi children-nya masih diperbarui -->
  <span>{{ $content }}</span>
</div>
```

---

## Reference

### Modifiers

| Modifier | Deskripsi                            |
| -------- | ------------------------------------ |
| `.self`  | Hanya abaikan elemen, bukan children |
