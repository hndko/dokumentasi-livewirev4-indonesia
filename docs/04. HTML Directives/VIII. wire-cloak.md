# wire:cloak

Direktif `wire:cloak` menyembunyikan elemen sampai Livewire diinisialisasi. Ini mencegah flash of unstyled content (FOUC).

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Dynamic Content](#dynamic-content)
- [Reference](#reference)

---

## Penggunaan Dasar

Livewire otomatis menyertakan CSS yang menyembunyikan elemen dengan atribut `wire:cloak`:

```html
<div wire:cloak>
  Konten yang akan disembunyikan sampai Livewire diinisialisasi
</div>
```

---

## Dynamic Content

Berguna untuk menyembunyikan konten yang bergantung pada Alpine atau JavaScript:

```html
<div wire:cloak x-data="{ show: false }">
  <button x-on:click="show = ! show">Toggle</button>
  <div x-show="show">Hidden content</div>
</div>
```

---

## Reference

`wire:cloak` tidak memiliki modifiers atau parameter.
