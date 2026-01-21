# wire:dirty

Direktif `wire:dirty` memungkinkan Anda menampilkan atau menyembunyikan elemen berdasarkan apakah ada perubahan yang belum disimpan (unsaved changes) di komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Targeting Property](#targeting-property)
- [Toggle Classes](#toggle-classes)
- [Ekspresi $dirty](#ekspresi-dirty)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<form wire:submit="save">
  <input wire:model="title" />

  <span wire:dirty>Unsaved changes...</span>

  <button type="submit">Save</button>
</form>
```

Pesan "Unsaved changes..." akan muncul saat pengguna mengubah input.

---

## Targeting Property

Target properti spesifik:

```html
<input wire:model="title" />
<span wire:dirty wire:target="title">Title changed</span>

<input wire:model="content" />
<span wire:dirty wire:target="content">Content changed</span>
```

---

## Toggle Classes

Gunakan `.class` untuk toggle class:

```html
<input wire:model="title" wire:dirty.class="border-yellow-500" />
```

---

## Ekspresi $dirty

Gunakan ekspresi `$dirty` di Alpine untuk kontrol lebih lanjut:

```html
<div x-data>
  <input wire:model="title" />

  <template x-if="$dirty">
    <span>Ada perubahan</span>
  </template>

  <template x-if="$dirty('title')">
    <span>Title berubah</span>
  </template>
</div>
```

---

## Reference

### Modifiers

| Modifier        | Deskripsi               |
| --------------- | ----------------------- |
| `.remove`       | Sembunyikan saat dirty  |
| `.class`        | Toggle class saat dirty |
| `.class.remove` | Hapus class saat dirty  |
