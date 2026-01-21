# Teleport

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Teleport memungkinkan Anda memindahkan bagian template Blade Anda ke bagian lain di DOM. Ini berguna untuk modal, dropdown, dan elemen lain yang perlu dirender di luar konteks parent mereka.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kasus Penggunaan](#kasus-penggunaan)

---

## Penggunaan Dasar

Gunakan direktif `@teleport` untuk memindahkan konten ke selector CSS manapun:

```html
<div>
  <button wire:click="showModal = true">Open Modal</button>

  @teleport('body')
  <div x-show="$wire.showModal" class="modal">
    <div class="modal-content">
      <h2>Modal Title</h2>
      <p>Modal content here...</p>
      <button wire:click="showModal = false">Close</button>
    </div>
  </div>
  @endteleport
</div>
```

Konten di dalam `@teleport` akan ditambahkan ke elemen `<body>` alih-alih tetap di dalam komponen.

---

## Kasus Penggunaan

### Modal

```html
<div>
  <button wire:click="showModal = true">Open Modal</button>

  @teleport('body') @if ($showModal)
  <div class="modal-overlay">
    <div class="modal-content">
      <h2>Konfirmasi</h2>
      <p>Apakah Anda yakin?</p>
      <button wire:click="confirm">Ya</button>
      <button wire:click="showModal = false">Batal</button>
    </div>
  </div>
  @endif @endteleport
</div>
```

### Dropdown dengan z-index

```html
<div class="relative">
  <button x-on:click="open = true">Menu</button>

  @teleport('body')
  <div x-show="open" x-on:click.away="open = false" class="dropdown-menu">
    <a href="#">Item 1</a>
    <a href="#">Item 2</a>
  </div>
  @endteleport
</div>
```

### Toast Notifications

```html
@teleport('#toast-container') @if (session('message'))
<div class="toast">{{ session('message') }}</div>
@endif @endteleport
```
