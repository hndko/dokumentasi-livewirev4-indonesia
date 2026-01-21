# Quickstart

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda membangun antarmuka yang dinamis dan reaktif hanya dengan menggunakan PHP — tanpa perlu JavaScript. Daripada menulis kode frontend menggunakan framework JavaScript, Anda cukup menulis class PHP sederhana dan template Blade, lalu Livewire akan menangani semua JavaScript yang kompleks di balik layar.

Sebagai demonstrasi, kita akan membuat form pembuatan post sederhana dengan validasi real-time. Anda akan melihat bagaimana Livewire dapat memvalidasi input dan memperbarui halaman secara dinamis tanpa menulis satu baris JavaScript pun atau menangani request AJAX secara manual.

---

## Daftar Isi

- [Prasyarat](#prasyarat)
- [Install Livewire](#install-livewire)
- [Membuat Layout](#membuat-layout)
- [Membuat Komponen Livewire](#membuat-komponen-livewire)
- [Menulis Komponen](#menulis-komponen)
- [Mendaftarkan Route](#mendaftarkan-route)
- [Uji Coba](#uji-coba)
- [Troubleshooting](#troubleshooting)
- [Langkah Selanjutnya](#langkah-selanjutnya)

---

## Prasyarat

Sebelum memulai, pastikan Anda sudah menginstall:

- **Laravel** versi 10 atau lebih baru
- **PHP** versi 8.1 atau lebih baru

---

## Install Livewire

Dari direktori root aplikasi Laravel Anda, jalankan perintah [Composer](https://getcomposer.org/) berikut:

```bash
composer require livewire/livewire
```

---

## Membuat Layout

Sebelum membuat komponen, mari kita siapkan file layout yang akan menjadi tempat komponen Livewire di-render. Secara default, Livewire mencari layout di:

```
resources/views/layouts/app.blade.php
```

Anda dapat membuat file ini dengan menjalankan perintah berikut:

```bash
php artisan livewire:layout
```

Perintah ini akan menghasilkan `resources/views/layouts/app.blade.php` dengan konten berikut:

```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <title>{{ $title ?? config('app.name') }}</title>

    @vite(['resources/css/app.css', 'resources/js/app.js']) @livewireStyles
  </head>
  <body>
    {{ $slot }} @livewireScripts
  </body>
</html>
```

Direktif `@livewireStyles` dan `@livewireScripts` menyertakan aset JavaScript dan CSS yang diperlukan agar Livewire dapat berfungsi. Komponen Anda akan di-render menggantikan variabel `{{ $slot }}`.

---

## Membuat Komponen Livewire

Livewire menyediakan perintah Artisan yang praktis untuk membuat komponen baru. Jalankan perintah berikut untuk membuat halaman komponen baru:

```bash
php artisan make:livewire pages::post.create
```

Karena komponen ini akan digunakan sebagai halaman penuh, kita menggunakan prefix `pages::` agar terorganisir di dalam direktori pages.

Perintah ini akan menghasilkan komponen single-file baru di:

```
resources/views/pages/post/⚡create.blade.php
```

> **Catatan:** Simbol petir (⚡) membuat komponen Livewire langsung dapat dikenali di editor Anda. Ini sepenuhnya opsional dan dapat dinonaktifkan di konfigurasi jika Anda mau. Lihat [dokumentasi komponen](https://livewire.laravel.com/docs/4.x/components#creating-components) untuk detail lebih lanjut.

---

## Menulis Komponen

Buka `resources/views/pages/post/⚡create.blade.php` dan ganti isinya dengan kode berikut:

```php
<?php

use Livewire\Component;

new class extends Component {
    public string $title = '';
    public string $content = '';

    public function save()
    {
        $this->validate([
            'title' => 'required|max:255',
            'content' => 'required',
        ]);

        dd($this->title, $this->content);
    }
};
?>

<form wire:submit="save">
    <label>
        Judul
        <input type="text" wire:model="title">
        @error('title')
            <span style="color: red;">{{ $message }}</span>
        @enderror
    </label>

    <label>
        Konten
        <textarea wire:model="content" rows="5"></textarea>
        @error('content')
            <span style="color: red;">{{ $message }}</span>
        @enderror
    </label>

    <button type="submit">Simpan Post</button>
</form>
```

Form ini sengaja dibuat tanpa styling agar kita bisa fokus pada fungsionalitas Livewire. Pada aplikasi nyata, Anda akan menambahkan CSS atau menggunakan framework seperti Tailwind.

### Penjelasan Kode

#### Property Komponen:

- `public string $title = '';` — Mendeklarasikan property public untuk judul post
- `public string $content = '';` — Mendeklarasikan property public untuk konten post

#### Method Komponen:

- `public function save()` — Dipanggil saat form di-submit. Memvalidasi data dan menampilkan output untuk pengujian.

#### Direktif Livewire:

- `wire:submit="save"` — Memanggil method `save()` saat form di-submit, mencegah reload halaman default
- `wire:model="title"` — Membuat binding data dua arah antara input dan property `$title`. Saat Anda mengetik, property akan diperbarui secara otomatis
- `wire:model="content"` — Binding dua arah yang sama untuk textarea dan property `$content`
- `@error('title')` dan `@error('content')` — Menampilkan pesan error validasi ketika validasi gagal

> **Penting:** Komponen harus memiliki tepat satu elemen HTML root. Dalam contoh ini, elemen `<form>` adalah root tunggal. Beberapa elemen root atau komentar HTML di luar elemen root akan menyebabkan error. Saat merender [komponen halaman penuh](https://livewire.laravel.com/docs/4.x/pages), named slot untuk layout dapat ditempatkan di luar elemen root.

### Versi Production

Method `save()` menggunakan `dd()` untuk menampilkan nilai untuk tujuan pengujian. Pada aplikasi production, Anda biasanya akan menyimpan data ke database dan melakukan redirect:

```php
public function save()
{
    $validated = $this->validate([
        'title' => 'required|max:255',
        'content' => 'required',
    ]);

    Post::create($validated); // Asumsikan Anda memiliki model Post dan tabel database

    return $this->redirect('/posts');
}
```

---

## Mendaftarkan Route

Buka file `routes/web.php` di aplikasi Laravel Anda dan tambahkan kode berikut:

```php
Route::livewire('/post/create', 'pages::post.create');
```

Sekarang ketika pengguna mengunjungi `/post/create`, Livewire akan merender komponen `pages::post.create` di dalam file layout Anda.

---

## Uji Coba

Dengan semua sudah siap, mari kita uji komponennya!

Jalankan server development Laravel jika belum berjalan:

```bash
php artisan serve
```

Kunjungi `http://localhost:8000/post/create` di browser Anda (atau `http://yourapp.test/post/create` jika menggunakan Valet, Herd, atau tool serupa).

Anda akan melihat form sederhana dengan dua field dan tombol submit.

### Coba hal berikut:

1. **Uji validasi:** Klik "Simpan Post" tanpa mengisi field apapun. Anda akan melihat pesan error merah muncul secara instan di bawah setiap field — tanpa perlu reload halaman.

2. **Uji submit:** Isi kedua field dan klik "Simpan Post". Anda akan melihat layar debug yang menampilkan nilai yang Anda masukkan.

Ini mendemonstrasikan kekuatan utama Livewire: binding data reaktif, validasi real-time, dan penanganan form — semuanya ditulis dalam PHP tanpa menyentuh JavaScript.

---

## Troubleshooting

### Error komponen tidak ditemukan:

- Pastikan file komponen ada di `resources/views/pages/post/⚡create.blade.php`
- Periksa apakah nama komponen di route sudah cocok: `'pages::post.create'`

### Form tidak submit atau validasi tidak muncul:

- Pastikan `@livewireStyles` ada di dalam `<head>` dan `@livewireScripts` ada sebelum `</body>` di layout Anda
- Periksa console browser untuk error JavaScript

### Error 404 saat mengunjungi route:

- Verifikasi bahwa route sudah ditambahkan ke `routes/web.php`

---

## Langkah Selanjutnya

Sekarang setelah Anda membuat komponen Livewire pertama, berikut beberapa konsep kunci untuk dijelajahi:

- [Components](https://livewire.laravel.com/docs/4.x/components) — Pelajari tentang komponen single-file vs multi-file, passing data, dan lainnya
- [Properties](https://livewire.laravel.com/docs/4.x/properties) — Pahami cara kerja property komponen dan lifecycle-nya
- [Actions](https://livewire.laravel.com/docs/4.x/actions) — Pelajari lebih dalam tentang method, parameter, dan event handling
- [Forms](https://livewire.laravel.com/docs/4.x/forms) — Jelajahi fitur form powerful milik Livewire termasuk validasi real-time
- [Validation](https://livewire.laravel.com/docs/4.x/validation) — Kuasai semua kemampuan validasi Livewire

Kita baru saja menyentuh permukaan dari apa yang bisa dilakukan Livewire. Terus baca dokumentasi untuk melihat semua yang ditawarkan Livewire!
