# wire:replace

Direktif `wire:replace` memaksa Livewire untuk mengganti elemen sepenuhnya alih-alih melakukan DOM diffing.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Self-only](#self-only)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:replace>
  <!-- Elemen ini akan diganti sepenuhnya -->
  {{ $content }}
</div>
```

Berguna saat Anda ingin memastikan elemen dirender ulang sepenuhnya.

---

## Self-only

Gunakan `.self` untuk hanya mengganti elemen itu sendiri:

```html
<div wire:replace.self>
  <span>{{ $content }}</span>
</div>
```

---

## Reference

### Modifiers

| Modifier | Deskripsi                          |
| -------- | ---------------------------------- |
| `.self`  | Hanya ganti elemen, bukan children |
