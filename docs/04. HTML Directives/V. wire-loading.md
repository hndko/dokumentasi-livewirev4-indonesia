# wire:loading

Loading indicators penting untuk membuat UI yang baik. Mereka memberikan feedback visual kepada pengguna saat request sedang dibuat ke server.

> **Tips:** Livewire v4 memperkenalkan atribut `data-loading` otomatis. Pendekatan ini sering lebih sederhana dan fleksibel.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menghapus Elemen](#menghapus-elemen)
- [Toggle Classes](#toggle-classes)
- [Toggle Attributes](#toggle-attributes)
- [Targeting Spesifik](#targeting-spesifik)
- [Reference](#reference)

---

## Penggunaan Dasar

`wire:loading` menyembunyikan elemen secara default dan menampilkannya saat request dikirim ke server:

```html
<form wire:submit="save">
  <!-- ... -->
  <button type="submit">Save</button>
  <div wire:loading>Saving post...</div>
</form>
```

---

## Menghapus Elemen

Gunakan `.remove` untuk efek sebaliknya - menampilkan elemen secara default dan menyembunyikannya selama request:

```html
<div wire:loading.remove>Konten normal</div>
```

---

## Toggle Classes

Gunakan `wire:loading.class` untuk toggle class CSS:

```html
<button wire:loading.class="opacity-50">Save</button>
```

Untuk menghapus class:

```html
<button class="bg-blue-500" wire:loading.class.remove="bg-blue-500">
  Save
</button>
```

---

## Toggle Attributes

Gunakan `.attr` untuk toggle atribut:

```html
<button type="button" wire:click="remove" wire:loading.attr="disabled">
  Remove
</button>
```

---

## Targeting Spesifik

Gunakan `wire:target` untuk membatasi loading indicator ke aksi tertentu:

```html
<form wire:submit="save">
  <button type="submit">Save</button>
  <button type="button" wire:click="remove">Remove</button>

  <div wire:loading wire:target="remove">Removing post...</div>
</form>
```

**Multiple targets:**

```html
<div wire:loading wire:target="save, remove">Loading...</div>
```

**Targeting parameter:**

```html
<div wire:loading wire:target="delete({{ $post->id }})">Deleting...</div>
```

**Targeting property updates:**

```html
<div wire:loading wire:target="title">Updating title...</div>
```

---

## Reference

### Modifiers

| Modifier        | Deskripsi                      |
| --------------- | ------------------------------ |
| `.remove`       | Sembunyikan saat loading       |
| `.class`        | Toggle class                   |
| `.class.remove` | Hapus class saat loading       |
| `.attr`         | Toggle attribute               |
| `.delay`        | Tunda tampilan (default 200ms) |
| `.delay.long`   | Tunda lebih lama (500ms)       |
| `.flex`         | Display flex saat loading      |
| `.grid`         | Display grid saat loading      |
| `.inline`       | Display inline saat loading    |
