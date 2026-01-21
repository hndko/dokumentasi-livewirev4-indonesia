# Hydration

Hydration adalah proses konversi state komponen antara PHP (server) dan JSON (client). Ini adalah mekanisme inti yang memungkinkan Livewire bekerja.

---

## Daftar Isi

- [Dehydrating](#dehydrating)
- [Snapshot](#snapshot)
- [Hydrating](#hydrating)
- [Advanced Hydration](#advanced-hydration)

---

## Dehydrating

Dehydration adalah proses mengkonversi state komponen PHP ke format yang dapat dikirim ke browser.

### Rendering HTML

```
PHP Component State → Blade Template → HTML Output
```

### Snapshot

Snapshot adalah representasi JSON dari state komponen:

```json
{
  "state": {
    "count": 5,
    "name": "John"
  },
  "memo": {
    "id": "abc123",
    "name": "counter",
    "path": "counter",
    "method": "GET"
  },
  "checksum": "..."
}
```

### Embedding Snapshot

Snapshot disertakan dalam HTML sebagai atribut `wire:snapshot`:

```html
<div wire:id="abc123" wire:snapshot="{...}">
  <!-- Component HTML -->
</div>
```

---

## Hydrating

Hydration adalah proses sebaliknya - mengkonversi snapshot JSON kembali ke komponen PHP.

```
JSON Snapshot → PHP Component Instance → Execute Action → Re-render
```

---

## Advanced Hydration

### Deeply Nested Tuples

Livewire menggunakan "tuple" untuk menyimpan tipe data kompleks:

```json
["__livewire_type__", "DateTime", "2024-01-21T10:00:00"]
```

### Supporting Custom Property Types

Untuk tipe properti kustom, gunakan Synthesizers (lihat dokumentasi Synthesizers).
