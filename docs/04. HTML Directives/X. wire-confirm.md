# wire:confirm

Direktif `wire:confirm` menampilkan dialog konfirmasi sebelum aksi dieksekusi.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Prompt Input](#prompt-input)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<button wire:click="delete" wire:confirm="Apakah Anda yakin ingin menghapus?">
  Delete
</button>
```

Dialog konfirmasi browser akan muncul sebelum method `delete` dieksekusi.

---

## Prompt Input

Gunakan `.prompt` untuk meminta input dari pengguna:

```html
<button
  wire:click="delete"
  wire:confirm.prompt="Ketik 'DELETE' untuk mengkonfirmasi|DELETE"
>
  Delete
</button>
```

Format: `Pesan prompt|Nilai yang diharapkan`

---

## Reference

### Modifiers

| Modifier  | Deskripsi              |
| --------- | ---------------------- |
| `.prompt` | Minta input konfirmasi |
