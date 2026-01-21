# wire:show

Direktif `wire:show` menampilkan atau menyembunyikan elemen berdasarkan ekspresi.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menggunakan Transitions](#menggunakan-transitions)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:show="$showModal">
  <div class="modal">Modal content</div>
</div>
```

Mirip dengan `x-show` di Alpine, tetapi menggunakan properti Livewire.

---

## Menggunakan Transitions

Kombinasikan dengan `wire:transition`:

```html
<div wire:show="$showModal" wire:transition>
  <div class="modal">Modal content</div>
</div>
```

---

## Reference

### Sintaks

```
wire:show="expression"
wire:show="$property"
```
