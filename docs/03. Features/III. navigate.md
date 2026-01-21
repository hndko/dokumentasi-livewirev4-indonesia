# Navigate

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Banyak aplikasi web modern dibangun sebagai "single page applications" (SPA). Dalam aplikasi ini, setiap halaman tidak lagi memerlukan reload halaman browser penuh, menghindari overhead mendownload ulang aset JavaScript dan CSS pada setiap request.

Livewire menawarkan pengalaman single page application melalui atribut sederhana yang dapat Anda tambahkan ke link: `wire:navigate`.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Redirects](#redirects)
- [Prefetching Links](#prefetching-links)
- [Mempertahankan Elemen Antar Kunjungan Halaman](#mempertahankan-elemen-antar-kunjungan-halaman)
- [Highlighting Link Aktif](#highlighting-link-aktif)
- [Mempertahankan Posisi Scroll](#mempertahankan-posisi-scroll)
- [JavaScript Hooks](#javascript-hooks)
- [Mengunjungi Halaman Baru Secara Manual](#mengunjungi-halaman-baru-secara-manual)
- [Evaluasi Script](#evaluasi-script)
- [Menyesuaikan Progress Bar](#menyesuaikan-progress-bar)

---

## Penggunaan Dasar

```php
// routes/web.php
Route::livewire('/', 'pages::dashboard');
Route::livewire('/posts', 'pages::show-posts');
Route::livewire('/users', 'pages::show-users');
```

Dengan menambahkan `wire:navigate` ke setiap link:

```html
<nav>
  <a href="/" wire:navigate>Dashboard</a>
  <a href="/posts" wire:navigate>Posts</a>
  <a href="/users" wire:navigate>Users</a>
</nav>
```

**Cara kerjanya saat link diklik:**

1. Pengguna mengklik link
2. Livewire mencegah browser mengunjungi halaman baru
3. Livewire meminta halaman di background dan menampilkan loading bar
4. Saat HTML halaman baru diterima, Livewire mengganti URL, tag `<title>` dan konten `<body>` dengan elemen dari halaman baru

Teknik ini menghasilkan waktu load halaman yang jauh lebih cepat — sering dua kali lebih cepat.

---

## Redirects

Untuk menggunakan navigasi wire:navigate pada redirect:

```php
return $this->redirect('/posts', navigate: true);
```

---

## Prefetching Links

Secara default, Livewire menyertakan strategi halus untuk prefetch halaman:

- Pengguna menekan tombol mouse
- Livewire mulai meminta halaman
- Pengguna mengangkat tombol mouse untuk menyelesaikan klik
- Livewire menyelesaikan request dan menavigasi ke halaman baru

Untuk pendekatan yang lebih agresif, gunakan modifier `.hover`:

```html
<a href="/posts" wire:navigate.hover>Posts</a>
```

Modifier `.hover` akan menginstruksikan Livewire untuk prefetch halaman setelah pengguna hover di atas link selama 60 milidetik.

---

## Mempertahankan Elemen Antar Kunjungan Halaman

Untuk mempertahankan elemen seperti audio atau video player antar halaman, gunakan direktif `@persist`:

```html
@persist('player')
<audio src="{{ $episode->file }}" controls></audio>
@endpersist
```

> **Penting:** Elemen yang dipertahankan harus ditempatkan di luar komponen Livewire Anda, biasanya di layout utama seperti `resources/views/layouts/app.blade.php`.

---

## Highlighting Link Aktif

### Menggunakan Atribut data-current

Livewire otomatis menambahkan atribut `data-current` ke link `wire:navigate` yang cocok dengan halaman saat ini:

```html
<nav>
  <a
    href="/dashboard"
    wire:navigate
    class="data-current:font-bold data-current:text-zinc-800"
    >Dashboard</a
  >
  <a
    href="/posts"
    wire:navigate
    class="data-current:font-bold data-current:text-zinc-800"
    >Posts</a
  >
</nav>
```

Dengan CSS biasa:

```css
[data-current] {
  font-weight: bold;
  color: #18181b;
}
```

### Menggunakan Direktif wire:current

```html
<nav>
  <a href="/dashboard" wire:navigate wire:current="font-bold text-zinc-800"
    >Dashboard</a
  >
  <a href="/posts" wire:navigate wire:current="font-bold text-zinc-800"
    >Posts</a
  >
</nav>
```

---

## Mempertahankan Posisi Scroll

```html
@persist('sidebar')
<div class="overflow-y-scroll" wire:navigate:scroll>
  <!-- ... -->
</div>
@endpersist
```

---

## JavaScript Hooks

Setiap navigasi halaman memicu tiga lifecycle hooks:

- `livewire:navigate` - Saat navigasi dipicu
- `livewire:navigating` - Saat HTML baru akan di-swap
- `livewire:navigated` - Sebagai langkah akhir navigasi

```javascript
document.addEventListener("livewire:navigate", (event) => {
  // Dapat dibatalkan:
  event.preventDefault();

  // Konteks:
  let context = event.detail;
  context.url; // URL tujuan
  context.history; // true jika navigasi back/forward
  context.cached; // true jika ada versi cached
});

document.addEventListener("livewire:navigated", () => {
  // Dipicu pada setiap navigasi halaman
  // dan pada page-load sebagai pengganti "DOMContentLoaded"
});
```

> **Tips:** Gunakan `{ once: true }` untuk event listener yang hanya perlu berjalan sekali:
>
> ```javascript
> document.addEventListener(
>   "livewire:navigated",
>   () => {
>     // ...
>   },
>   { once: true },
> );
> ```

---

## Mengunjungi Halaman Baru Secara Manual

```javascript
Livewire.navigate("/new/url");
```

---

## Evaluasi Script

### Jangan Andalkan DOMContentLoaded

Ganti `DOMContentLoaded` dengan `livewire:navigated`:

```javascript
// Sebelum
document.addEventListener('DOMContentLoaded', () => { ... })

// Sesudah
document.addEventListener('livewire:navigated', () => { ... })
```

### Script di `<head>` Dimuat Sekali

Script yang sama di `<head>` hanya dijalankan pada kunjungan halaman awal.

### Reload Saat Aset Berubah

Tambahkan `data-navigate-track` untuk memicu reload saat aset berubah:

```html
<script src="/app.js?id=123" data-navigate-track></script>
```

Jika menggunakan Vite, Livewire otomatis menambahkan `data-navigate-track`.

### Script di `<body>` Di-evaluasi Ulang

Script di body dijalankan ulang pada setiap halaman. Gunakan `data-navigate-once` untuk script yang hanya perlu dijalankan sekali:

```html
<script data-navigate-once>
  console.log("Hanya berjalan sekali");
</script>
```

---

## Menyesuaikan Progress Bar

Di `config/livewire.php`:

```php
'navigate' => [
    'show_progress_bar' => false,
    'progress_bar_color' => '#2299dd',
],
```
