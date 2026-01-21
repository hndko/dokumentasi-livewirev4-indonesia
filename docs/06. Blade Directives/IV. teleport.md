# @teleport

Direktif `@teleport` memindahkan konten ke lokasi lain di DOM, berguna untuk modal, dropdown, dan elemen yang perlu dirender di luar konteks parent.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Mengapa Menggunakan Teleport](#mengapa-menggunakan-teleport)
- [Kasus Penggunaan Umum](#kasus-penggunaan-umum)
- [Batasan Penting](#batasan-penting)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div>
  <button wire:click="showModal = true">Open Modal</button>

  @teleport('body')
  <div x-show="$wire.showModal" class="modal-overlay">
    <div class="modal">
      <h2>Modal Title</h2>
      <button wire:click="showModal = false">Close</button>
    </div>
  </div>
  @endteleport
</div>
```

---

## Mengapa Menggunakan Teleport

- **z-index issues**: Modal perlu berada di atas semua elemen
- **Overflow clipping**: Parent dengan `overflow: hidden` akan memotong dropdown
- **CSS stacking context**: Menghindari konflik stacking context

---

## Kasus Penggunaan Umum

### Modal

```html
@teleport('body') @if ($showModal)
<div class="fixed inset-0 bg-black/50 flex items-center justify-center">
  <div class="bg-white p-6 rounded">
    <!-- Modal content -->
  </div>
</div>
@endif @endteleport
```

### Dropdown

```html
@teleport('body')
<div x-show="open" class="fixed" :style="dropdownPosition">
  <a href="#">Item 1</a>
  <a href="#">Item 2</a>
</div>
@endteleport
```

### Toast Notifications

```html
@teleport('#toast-container') @if (session('message'))
<div class="toast">{{ session('message') }}</div>
@endif @endteleport
```

---

## Batasan Penting

- Target selector harus ada di DOM saat render
- Konten masih milik komponen Livewire asli
- Menggunakan Alpine's `x-teleport` di belakang layar

---

## Reference

| Parameter  | Deskripsi                                        |
| ---------- | ------------------------------------------------ |
| `selector` | CSS selector tujuan (e.g., 'body', '#container') |
