# Installation

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire adalah package Laravel, jadi Anda perlu memiliki aplikasi Laravel yang sudah berjalan sebelum dapat menginstall dan menggunakan Livewire. Jika Anda membutuhkan bantuan untuk setup aplikasi Laravel baru, silakan lihat [dokumentasi resmi Laravel](https://laravel.com/docs/installation).

---

## Daftar Isi

- [Prasyarat](#prasyarat)
- [Install Livewire](#install-livewire)
- [Membuat File Layout](#membuat-file-layout)
- [Mempublish File Konfigurasi](#mempublish-file-konfigurasi)
- [Konfigurasi Lanjutan](#konfigurasi-lanjutan)
  - [Bundling Livewire dan Alpine Secara Manual](#bundling-livewire-dan-alpine-secara-manual)
  - [Mengkustomisasi Endpoint Update Livewire](#mengkustomisasi-endpoint-update-livewire)
  - [Mengkustomisasi URL Asset JavaScript](#mengkustomisasi-url-asset-javascript)
  - [Mempublish Asset Livewire ke Direktori Public](#mempublish-asset-livewire-ke-direktori-public)
  - [Menonaktifkan Injeksi Asset Otomatis](#menonaktifkan-injeksi-asset-otomatis)
- [Troubleshooting](#troubleshooting)

---

## Prasyarat

Sebelum menginstall Livewire, pastikan Anda memiliki:

- **Laravel** versi 10 atau lebih baru
- **PHP** versi 8.1 atau lebih baru

---

## Install Livewire

Untuk menginstall Livewire, buka terminal dan navigasi ke direktori aplikasi Laravel Anda, lalu jalankan perintah berikut:

```bash
composer require livewire/livewire
```

Selesai! Livewire menggunakan fitur auto-discovery package Laravel, jadi tidak diperlukan setup tambahan.

Siap membuat komponen pertama Anda? Kunjungi [panduan Quickstart](./I.%20quick-start.md) untuk membuat komponen Livewire pertama dalam hitungan menit.

---

## Membuat File Layout

Saat menggunakan komponen Livewire sebagai halaman penuh, Anda memerlukan file layout. Anda dapat membuatnya menggunakan perintah Livewire:

```bash
php artisan livewire:layout
```

Ini akan membuat file layout di `resources/views/layouts/app.blade.php` dengan konten berikut:

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

Direktif `@livewireStyles` dan `@livewireScripts` menyertakan asset JavaScript dan CSS yang diperlukan agar Livewire berfungsi. Livewire membundel Alpine.js dengan JavaScriptnya, jadi keduanya dimuat bersamaan.

Bahkan tanpa direktif ini, Livewire akan otomatis menyuntikkan assetnya ke halaman yang berisi komponen Livewire. Namun, menyertakan direktif memberi Anda kontrol eksplisit tentang di mana asset ditempatkan, yang dapat membantu untuk optimasi performa atau kompatibilitas dengan package lain.

---

## Mempublish File Konfigurasi

Livewire bersifat "zero-config", artinya Anda dapat menggunakannya dengan mengikuti konvensi tanpa konfigurasi tambahan. Namun, jika diperlukan, Anda dapat mempublish dan mengkustomisasi file konfigurasi Livewire:

```bash
php artisan livewire:config
```

Ini akan membuat file `livewire.php` baru di direktori `config` aplikasi Laravel Anda untuk mengkustomisasi berbagai pengaturan Livewire.

---

# Konfigurasi Lanjutan

Bagian berikut mencakup skenario lanjutan yang sebagian besar aplikasi tidak memerlukan. Konfigurasikan ini hanya jika Anda memiliki kebutuhan khusus.

---

## Bundling Livewire dan Alpine Secara Manual

**Kapan Anda membutuhkan ini:** Jika Anda ingin menggunakan plugin Alpine.js atau memerlukan kontrol detail atas kapan Alpine dan Livewire diinisialisasi.

Secara default, Livewire otomatis memuat Alpine.js yang dibundel dengan JavaScriptnya. Namun, jika Anda perlu mendaftarkan plugin Alpine atau mengkustomisasi urutan inisialisasi, Anda dapat membundel Livewire dan Alpine secara manual menggunakan tool build JavaScript Anda.

Pertama, tambahkan direktif `@livewireScriptConfig` ke file layout Anda:

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
    {{ $slot }} @livewireScriptConfig
  </body>
</html>
```

Direktif `@livewireScriptConfig` menyuntikkan konfigurasi dan globals runtime yang dibutuhkan Livewire, tetapi tanpa JavaScript Livewire dan Alpine yang sebenarnya (karena Anda membundelnya sendiri). Ganti `@livewireScripts` dengan `@livewireScriptConfig` saat membundel secara manual.

Selanjutnya, import dan mulai Livewire dan Alpine di file `resources/js/app.js` Anda:

```javascript
import {
  Livewire,
  Alpine,
} from "../../vendor/livewire/livewire/dist/livewire.esm";
import Clipboard from "@ryangjchandler/alpine-clipboard";

Alpine.plugin(Clipboard);

Livewire.start();
```

> **Catatan:** Saat membundel secara manual, ingat untuk rebuild asset JavaScript Anda (`npm run build`) setiap kali Anda mengupdate Livewire via Composer.

---

## Mengkustomisasi Endpoint Update Livewire

**Kapan Anda membutuhkan ini:** Jika aplikasi Anda menggunakan prefix route untuk lokalisasi (seperti `/en/`, `/fr/`) atau multi-tenancy (seperti `/tenant-1/`, `/tenant-2/`), Anda mungkin perlu mengkustomisasi endpoint update Livewire agar sesuai dengan struktur routing Anda.

Secara default, Livewire mengirim update komponen ke endpoint berbasis hash seperti `/livewire-{hash}/update`, di mana `{hash}` berasal dari `APP_KEY` aplikasi Anda. Untuk mengkustomisasi ini, daftarkan route Anda sendiri di service provider (biasanya `App\Providers\AppServiceProvider`):

```php
use Livewire\Livewire;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Livewire::setUpdateRoute(function ($handle) {
            return Route::post('/custom/livewire/update', $handle);
        });
    }
}
```

Anda juga dapat menambahkan middleware ke route update:

```php
Livewire::setUpdateRoute(function ($handle) {
    return Route::post('/custom/livewire/update', $handle)
        ->middleware(['web', 'auth']);
});
```

---

## Mengkustomisasi URL Asset JavaScript

**Kapan Anda membutuhkan ini:** Jika aplikasi Anda menggunakan prefix route untuk lokalisasi atau multi-tenancy, Anda mungkin perlu mengkustomisasi di mana Livewire menyajikan JavaScriptnya agar sesuai dengan struktur routing Anda.

Secara default, Livewire menyajikan JavaScriptnya dari endpoint berbasis hash seperti `/livewire-{hash}/livewire.js`. Path unik per-instalasi ini membuatnya lebih sulit untuk menargetkan aplikasi Livewire dengan scanner otomatis.

Untuk mengkustomisasi ini, daftarkan route Anda sendiri di service provider:

```php
use Livewire\Livewire;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Livewire::setScriptRoute(function ($handle) {
            return Route::get('/custom/livewire/livewire.js', $handle);
        });
    }
}
```

---

## Mempublish Asset Livewire ke Direktori Public

**Kapan Anda membutuhkan ini:** Jika Anda ingin menyajikan JavaScript Livewire melalui web server secara langsung (misalnya, untuk distribusi CDN atau strategi caching tertentu) alih-alih melalui routing Laravel.

Anda dapat mempublish asset JavaScript Livewire ke direktori `public` Anda:

```bash
php artisan livewire:publish --assets
```

Untuk memastikan asset tetap up-to-date saat Anda mengupdate Livewire, tambahkan ini ke `composer.json` Anda:

```json
{
  "scripts": {
    "post-update-cmd": [
      "@php artisan vendor:publish --tag=livewire:assets --ansi --force"
    ]
  }
}
```

> **Catatan:** Mempublish asset jarang diperlukan. Lakukan ini hanya jika Anda memiliki kebutuhan arsitektur tertentu yang mencegah Laravel menyajikan asset secara dinamis.

---

## Menonaktifkan Injeksi Asset Otomatis

**Kapan Anda membutuhkan ini:** Jika Anda ingin kontrol penuh atas kapan dan bagaimana asset Livewire dimuat.

Update opsi konfigurasi `inject_assets` di file `config/livewire.php` Anda:

```php
'inject_assets' => false,
```

Saat dinonaktifkan, Anda harus menyertakan `@livewireStyles` dan `@livewireScripts` secara manual di layout Anda, atau Livewire tidak akan berfungsi.

Alternatifnya, Anda dapat memaksa injeksi asset pada halaman tertentu:

```php
\Livewire\Livewire::forceAssetInjection();
```

Panggil ini di route atau controller di mana Anda ingin memastikan asset disuntikkan.

---

# Troubleshooting

## JavaScript Livewire tidak loading (error 404)

**Gejala:** File JavaScript Livewire mengembalikan error 404, atau fitur Livewire tidak berfungsi.

Livewire menyajikan JavaScriptnya dari endpoint berbasis hash seperti `/livewire-{hash}/livewire.js`. Path unik ini bervariasi per instalasi.

### Penyebab umum:

**Konfigurasi Nginx memblokir route:** Jika Anda menggunakan Nginx dengan konfigurasi kustom, mungkin memblokir route dinamis Livewire. Anda dapat:

- Konfigurasikan Nginx untuk meneruskan request yang cocok `/livewire-*/` ke Laravel:
  ```nginx
  location ~ ^/livewire-[a-f0-9]+/ {
      try_files $uri $uri/ /index.php?$query_string;
  }
  ```
- [Bundel Livewire secara manual](#bundling-livewire-dan-alpine-secara-manual) untuk menghindari penyajian melalui Laravel
- [Publish asset Livewire](#mempublish-asset-livewire-ke-direktori-public) untuk menyajikannya langsung dari web server

**Route caching:** Jika Anda telah menjalankan `php artisan route:cache`, Laravel mungkin tidak mengenali route Livewire. Bersihkan cache:

```bash
php artisan route:clear
```

**@livewireScripts hilang:** Jika Anda telah menonaktifkan injeksi asset otomatis, pastikan `@livewireScripts` ada di file layout Anda sebelum `</body>`.

---

## Alpine.js tidak tersedia di halaman tanpa komponen Livewire

**Gejala:** Anda ingin menggunakan Alpine.js di halaman yang tidak memiliki komponen Livewire.

**Solusi:** Karena Alpine dibundel dengan Livewire, Anda perlu menyertakan `@livewireScripts` bahkan di halaman tanpa komponen Livewire:

```html
<!DOCTYPE html>
<html>
  <head>
    @livewireStyles
  </head>
  <body>
    <!-- Tidak ada komponen Livewire, tapi kita ingin Alpine -->
    <div x-data="{ open: false }">
      <button @click="open = !open">Toggle</button>
    </div>

    @livewireScripts
  </body>
</html>
```

Alternatifnya, [bundel Livewire dan Alpine secara manual](#bundling-livewire-dan-alpine-secara-manual) dan import Alpine di JavaScript Anda.

---

## Komponen tidak update atau error di console browser

Periksa hal berikut:

- Pastikan `@livewireStyles` ada di `<head>` layout Anda
- Pastikan `@livewireScripts` ada sebelum `</body>` di layout Anda
- Periksa console developer browser Anda untuk error JavaScript
- Verifikasi Anda menjalankan versi PHP yang didukung (8.1+) dan versi Laravel (10+)
- Bersihkan cache aplikasi: `php artisan cache:clear`

Jika masalah berlanjut, periksa [dokumentasi troubleshooting](https://livewire.laravel.com/docs/4.x/troubleshooting) untuk langkah debugging lebih detail.
