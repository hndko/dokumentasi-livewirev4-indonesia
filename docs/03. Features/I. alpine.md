# Alpine

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

[AlpineJS](https://alpinejs.dev/) adalah library JavaScript ringan yang memudahkan penambahan interaktivitas sisi klien ke halaman web Anda. Awalnya dibangun untuk melengkapi alat seperti Livewire di mana utilitas yang lebih berfokus JavaScript berguna untuk menambahkan interaktivitas ke aplikasi Anda.

Livewire sudah menyertakan Alpine secara bawaan sehingga tidak perlu menginstalnya secara terpisah ke proyek Anda.

---

## Daftar Isi

- [Komponen Alpine Dasar](#komponen-alpine-dasar)
- [Menggunakan Alpine di dalam Livewire](#menggunakan-alpine-di-dalam-livewire)
- [Mengontrol Livewire dari Alpine menggunakan $wire](#mengontrol-livewire-dari-alpine-menggunakan-wire)
  - [Mengakses Properti Livewire](#mengakses-properti-livewire)
  - [Memodifikasi Properti Livewire](#memodifikasi-properti-livewire)
  - [Memanggil Method Livewire](#memanggil-method-livewire)
  - [Merefresh Komponen](#merefresh-komponen)
- [Berbagi State Menggunakan $wire.entangle](#berbagi-state-menggunakan-wireentangle)
- [Menggunakan Direktif @js](#menggunakan-direktif-js)
- [Bundling Alpine Secara Manual](#bundling-alpine-secara-manual)

---

## Komponen Alpine Dasar

Berikut adalah contoh komponen Alpine paling sederhana. Counter kecil yang menampilkan angka dan memungkinkan pengguna menambah angka tersebut dengan mengklik tombol:

```html
<!-- Deklarasikan objek JavaScript dari data... -->
<div x-data="{ count: 0 }">
  <!-- Render nilai "count" saat ini di dalam elemen... -->
  <h2 x-text="count"></h2>

  <!-- Tambah nilai "count" dengan "1" saat event click dipicu... -->
  <button x-on:click="count++">+</button>
</div>
```

Komponen Alpine di atas dapat digunakan di dalam komponen Livewire manapun di aplikasi Anda. Livewire menangani pemeliharaan state Alpine di seluruh update komponen Livewire.

---

## Menggunakan Alpine di dalam Livewire

Mari jelajahi contoh nyata penggunaan komponen Alpine di dalam komponen Livewire.

```html
<div>
  <h1>{{ $post->title }}</h1>

  <div x-data="{ expanded: false }">
    <button type="button" x-on:click="expanded = ! expanded">
      <span x-show="! expanded">Tampilkan konten post...</span>
      <span x-show="expanded">Sembunyikan konten post...</span>
    </button>

    <div x-show="expanded">{{ $post->content }}</div>
  </div>
</div>
```

Dengan menggunakan Alpine, kita dapat menyembunyikan konten post sampai pengguna menekan tombol. Ini adalah contoh di mana Alpine bersinar: menambahkan interaktivitas ke aplikasi Anda tanpa memicu roundtrip server Livewire.

---

## Mengontrol Livewire dari Alpine Menggunakan $wire

Salah satu fitur paling powerful adalah `$wire`. Objek `$wire` adalah objek magic yang tersedia untuk semua komponen Alpine Anda yang digunakan di dalam Livewire.

### Mengakses Properti Livewire

Contoh utilitas "character count" dalam form:

```html
<form wire:submit="save">
  <input wire:model="content" type="text" />

  <small> Jumlah karakter: <span x-text="$wire.content.length"></span> </small>

  <button type="submit">Save</button>
</form>
```

### Memodifikasi Properti Livewire

Contoh menggunakan `$wire` untuk mengosongkan field "title":

```html
<form wire:submit="save">
  <input wire:model="title" type="text" />

  <button type="button" x-on:click="$wire.title = ''">Clear</button>

  <button type="submit">Save</button>
</form>
```

Penjelasan:

- `x-on:click` memberitahu Alpine untuk mendengarkan klik pada elemen button
- Saat diklik, Alpine menjalankan ekspresi JS: `$wire.title = ''`
- `$wire` adalah objek magic yang merepresentasikan komponen Livewire
- Interaksi akan "instan" tanpa network request

### Memanggil Method Livewire

Alpine juga dapat dengan mudah memanggil method/aksi Livewire:

```html
<form wire:submit="save">
  <input wire:model="title" type="text" x-on:blur="$wire.save()" />
  <button type="submit">Save</button>
</form>
```

**Passing Parameter:**

```html
@foreach ($posts as $post)
<button
  type="button"
  wire:key="{{ $post->id }}"
  x-on:click="$wire.deletePost({{ $post->id }})"
>
  Delete "{{ $post->title }}"
</button>
@endforeach
```

> **Perhatian Blade:** Untuk string seperti UUID, tambahkan tanda kutip:
>
> ```html
> <button x-on:click="$wire.deletePost('{{ $post->uuid }}')"></button>
> ```

### Merefresh Komponen

```html
<button type="button" x-on:click="$wire.$refresh()"></button>
```

---

## Berbagi State Menggunakan $wire.entangle

> **Catatan:** Dalam hampir semua kasus, gunakan `$wire` untuk mengakses properti Livewire secara langsung daripada `$wire.entangle()`. API ini dipertahankan untuk kompatibilitas mundur tetapi tidak disarankan untuk kode baru.

Untuk kasus langka di mana Anda membutuhkan sinkronisasi state dua arah:

```html
<div x-data="{ open: $wire.entangle('showDropdown') }">
  <button x-on:click="open = true">Show More...</button>

  <ul x-show="open">
    <li><button wire:click="archive">Archive</button></li>
  </ul>
</div>
```

Gunakan `.live` untuk sinkronisasi langsung:

```html
<div x-data="{ open: $wire.entangle('showDropdown').live }"></div>
```

---

## Menggunakan Direktif @js

Jika Anda perlu output data PHP untuk digunakan di Alpine langsung:

```html
<div x-data="{ posts: @js($posts) }">...</div>
```

---

## Bundling Alpine Secara Manual

Untuk menyertakan Livewire dan Alpine via bundle JavaScript sendiri:

1. Tambahkan `@livewireScriptConfig` di layout:

```html
<html>
  <head>
    @livewireStyles @vite(['resources/js/app.js'])
  </head>
  <body>
    {{ $slot }} @livewireScriptConfig
  </body>
</html>
```

2. Import Livewire dan Alpine di `resources/js/app.js`:

```javascript
import {
  Livewire,
  Alpine,
} from "../../vendor/livewire/livewire/dist/livewire.esm";

// Daftarkan direktif, komponen, atau plugin Alpine di sini...
Alpine.directive("clipboard", (el) => {
  let text = el.textContent;

  el.addEventListener("click", () => {
    navigator.clipboard.writeText(text);
  });
});

Livewire.start();
```
