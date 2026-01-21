# Components

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Komponen Livewire pada dasarnya adalah class PHP dengan properti dan method yang dapat dipanggil langsung dari template Blade. Kombinasi powerful ini memungkinkan Anda membuat antarmuka interaktif full-stack dengan upaya dan kompleksitas yang jauh lebih sedikit dibandingkan alternatif JavaScript modern.

Panduan ini mencakup semua yang perlu Anda ketahui tentang membuat, merender, dan mengorganisir komponen Livewire. Anda akan mempelajari berbagai format komponen yang tersedia (single-file, multi-file, dan berbasis class), cara passing data antar komponen, dan cara menggunakan komponen sebagai halaman penuh.

---

## Daftar Isi

- [Membuat Komponen](#membuat-komponen)
  - [Membuat Komponen Halaman](#membuat-komponen-halaman)
  - [Komponen Multi-file](#komponen-multi-file)
  - [Konversi Antar Format](#konversi-antar-format)
  - [Kapan Menggunakan Setiap Format](#kapan-menggunakan-setiap-format)
- [Merender Komponen](#merender-komponen)
  - [Passing Props](#passing-props)
  - [Passing Parameter Route sebagai Props](#passing-parameter-route-sebagai-props)
- [Komponen Halaman](#komponen-halaman)
- [Mengakses Data di Views](#mengakses-data-di-views)
  - [Property Komponen](#property-komponen)
  - [Computed Properties](#computed-properties)
  - [Passing Data dari render()](#passing-data-dari-render)
- [Mengorganisir Komponen](#mengorganisir-komponen)
  - [Namespace Komponen](#namespace-komponen)
  - [Lokasi Komponen Tambahan](#lokasi-komponen-tambahan)
  - [Registrasi Programatik](#registrasi-programatik)
- [Komponen Berbasis Class](#komponen-berbasis-class)
- [Mengkustomisasi Stub Komponen](#mengkustomisasi-stub-komponen)
- [Troubleshooting](#troubleshooting)

---

## Membuat Komponen

Anda dapat membuat komponen menggunakan perintah Artisan `make:livewire`:

```bash
php artisan make:livewire post.create
```

Ini membuat komponen single-file di:

```
resources/views/components/post/⚡create.blade.php
```

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title = '';

    public function save()
    {
        // Logika save di sini...
    }
};
?>

<div>
    <input wire:model="title" type="text">
    <button wire:click="save">Simpan Post</button>
</div>
```

Anda mungkin bertanya-tanya tentang petir di nama file. Sentuhan kecil ini memiliki tujuan praktis: membuat komponen Livewire langsung dapat dikenali di file tree dan hasil pencarian editor Anda. Karena ini adalah karakter Unicode, bekerja seamless di semua platform — Windows, macOS, Linux, Git, dan server production Anda.

Emoji sepenuhnya opsional dan jika Anda merasa tidak nyaman, Anda dapat menonaktifkannya sepenuhnya di file `config/livewire.php`:

```php
'make_command' => [
    'emoji' => false,
],
```

---

### Membuat Komponen Halaman

Saat membuat komponen yang akan digunakan sebagai halaman penuh, gunakan namespace `pages::` untuk mengorganisirnya di direktori khusus:

```bash
php artisan make:livewire pages::post.create
```

Ini membuat komponen di `resources/views/pages/post/⚡create.blade.php`. Organisasi ini memperjelas komponen mana yang merupakan halaman versus komponen UI yang dapat digunakan kembali.

Pelajari lebih lanjut tentang menggunakan komponen sebagai halaman di [bagian Komponen halaman](#komponen-halaman) di bawah. Anda juga dapat mendaftarkan namespace kustom Anda sendiri — lihat [dokumentasi Namespace komponen](https://livewire.laravel.com/docs/4.x/pages#component-namespaces).

---

### Komponen Multi-file

Saat komponen atau proyek Anda berkembang, Anda mungkin menemukan pendekatan single-file terbatas. Livewire menawarkan alternatif multi-file yang memisahkan komponen Anda menjadi file terpisah untuk organisasi dan dukungan IDE yang lebih baik.

Untuk membuat komponen multi-file, tambahkan flag `--mfc`:

```bash
php artisan make:livewire post.create --mfc
```

Ini membuat direktori dengan semua file terkait bersama:

```
resources/views/components/post/⚡create/
├── create.php           # Class PHP
├── create.blade.php     # Template Blade
├── create.js            # JavaScript (opsional)
├── create.css           # Scoped styles (opsional)
├── create.global.css    # Global styles (opsional)
└── create.test.php      # Pest test (opsional, dengan flag --test)
```

---

### Konversi Antar Format

Livewire menyediakan perintah `livewire:convert` untuk mengonversi komponen antara format single-file dan multi-file secara seamless.

**Auto-detect dan konversi:**

```bash
php artisan livewire:convert post.create
# Single-file → Multi-file (atau sebaliknya)
```

**Konversi eksplisit ke multi-file:**

```bash
php artisan livewire:convert post.create --mfc
```

**Konversi eksplisit ke single-file:**

```bash
php artisan livewire:convert post.create --sfc
```

> **Catatan:** Jika komponen multi-file Anda memiliki file test, Anda akan diminta konfirmasi sebelum konversi karena file test tidak dapat dipertahankan dalam format single-file.

---

### Kapan Menggunakan Setiap Format

**Komponen Single-file (default):**

- Terbaik untuk sebagian besar komponen
- Menjaga kode terkait tetap bersama
- Mudah dipahami sekilas
- Sempurna untuk komponen kecil hingga menengah

**Komponen Multi-file:**

- Lebih baik untuk komponen besar dan kompleks
- Dukungan IDE dan navigasi yang lebih baik
- Pemisahan lebih jelas ketika komponen memiliki JavaScript signifikan

**Komponen Berbasis Class:**

- Familiar bagi developer dari Livewire v2/v3
- Pemisahan concerns tradisional Laravel
- Lebih baik untuk tim dengan konvensi yang sudah mapan
- Lihat [Komponen berbasis class](#komponen-berbasis-class) di bawah

---

## Merender Komponen

Anda dapat menyertakan komponen Livewire dalam template Blade manapun menggunakan syntax `<livewire:component-name />`:

```html
<livewire:component-name />
```

Jika komponen terletak di sub-direktori, Anda dapat menunjukkannya menggunakan karakter titik (`.`):

```html
<!-- resources/views/components/post/⚡create.blade.php -->
<livewire:post.create />
```

Untuk komponen dengan namespace — seperti `pages::` — gunakan prefix namespace:

```html
<livewire:pages::post.create />
```

---

### Passing Props

Untuk passing data ke komponen Livewire, Anda dapat menggunakan atribut prop pada tag komponen:

```html
<livewire:post.create title="Judul Awal" />
```

Untuk nilai dinamis atau variabel, awali atribut dengan titik dua:

```html
<livewire:post.create :title="$initialTitle" />
```

Data yang di-pass ke komponen diterima melalui method `mount()`:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title;

    public function mount($title = null)
    {
        $this->title = $title;
    }

    // ...
};
```

Anda dapat menganggap method `mount()` sebagai constructor class. Ini berjalan saat komponen diinisialisasi, tetapi tidak pada request berikutnya dalam sesi halaman. Anda dapat mempelajari lebih lanjut tentang `mount()` dan lifecycle hooks bermanfaat lainnya dalam [dokumentasi lifecycle](./IX.%20lifecycle-hooks.md).

Untuk mengurangi kode boilerplate, Anda dapat menghilangkan method `mount()` dan Livewire akan otomatis menyetel properti dengan nama yang cocok dengan nilai yang di-pass:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title; // Otomatis disetel dari prop

    // ...
};
```

> **Catatan:** Properti `$title` tidak akan update otomatis jika `:title="$initialValue"` luar berubah setelah page load awal. Ini adalah poin kebingungan umum saat menggunakan Livewire, terutama bagi developer yang telah menggunakan framework JavaScript seperti Vue atau React dan mengasumsikan parameter ini berperilaku seperti "reactive props" di framework tersebut. Tapi jangan khawatir, Livewire memungkinkan Anda untuk opt-in ke [membuat props Anda reactive](https://livewire.laravel.com/docs/4.x/nesting#reactive-props).

---

### Passing Parameter Route sebagai Props

Saat menggunakan komponen sebagai halaman, Anda dapat passing parameter route langsung ke komponen. Parameter route otomatis di-pass ke method `mount()`:

```php
Route::livewire('/posts/{id}', 'pages::post.show');
```

```php
<?php
// resources/views/pages/post/⚡show.blade.php

use Livewire\Component;

new class extends Component {
    public $postId;

    public function mount($id)
    {
        $this->postId = $id;
    }
};
```

Livewire juga mendukung route model binding Laravel:

```php
Route::livewire('/posts/{post}', 'pages::post.show');
```

```php
<?php
// resources/views/pages/post/⚡show.blade.php

use App\Models\Post;
use Livewire\Component;

new class extends Component {
    public Post $post; // Otomatis di-bind dari route

    // Tidak perlu mount() - Livewire menanganinya otomatis
};
```

---

## Komponen Halaman

Komponen dapat di-route langsung sebagai halaman penuh menggunakan `Route::livewire()`. Ini adalah salah satu fitur paling powerful Livewire, memungkinkan Anda membangun seluruh halaman tanpa controller tradisional.

```php
Route::livewire('/posts/create', 'pages::post.create');
```

Saat pengguna mengunjungi `/posts/create`, Livewire akan merender komponen `pages::post.create` di dalam file layout aplikasi Anda.

Komponen halaman bekerja seperti komponen biasa, tetapi mereka di-render sebagai halaman penuh dengan akses ke:

- Layout kustom
- Judul halaman
- Parameter route dan model binding
- Named slots untuk layout

Untuk informasi lengkap tentang komponen halaman, termasuk layout, judul, dan routing lanjutan, lihat [dokumentasi Pages](./V.%20pages.md).

---

## Mengakses Data di Views

Livewire menyediakan beberapa cara untuk passing data ke view Blade komponen Anda. Setiap pendekatan memiliki karakteristik performa dan keamanan yang berbeda.

### Property Komponen

Pendekatan paling sederhana adalah menggunakan properti public, yang otomatis tersedia di template Blade Anda:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title = 'Post Saya';
};
```

```html
<div>
  <h1>{{ $title }}</h1>
</div>
```

Properti protected harus diakses dengan `$this->`:

```php
public $title = 'Post Saya';           // Tersedia sebagai {{ $title }}
protected $apiKey = 'secret-key';      // Tersedia sebagai {{ $this->apiKey }}
```

Berbeda dengan properti public, properti protected tidak pernah dikirim ke frontend dan tidak dapat dimanipulasi pengguna. Ini membuatnya aman untuk data sensitif. Namun, mereka tidak dipersist antar request, yang membatasi kegunaannya dalam sebagian besar skenario Livewire. Mereka paling baik digunakan untuk nilai statis yang ditentukan dalam deklarasi properti yang tidak ingin Anda ekspos ke sisi klien.

Untuk informasi lengkap tentang properti, termasuk perilaku persistence dan fitur lanjutan, lihat [dokumentasi properties](./VI.%20properties.md).

---

### Computed Properties

Computed properties adalah method yang bertindak seperti properti yang di-memoize. Mereka sempurna untuk operasi mahal seperti query database:

```php
use Livewire\Attributes\Computed;

#[Computed]
public function posts()
{
    return Post::with('author')->latest()->get();
}
```

```html
<div>
  @foreach ($this->posts as $post)
  <article wire:key="{{ $post->id }}">{{ $post->title }}</article>
  @endforeach
</div>
```

Perhatikan prefix `$this->` — ini memberitahu Livewire untuk memanggil method dan meng-cache hasilnya hanya untuk request saat ini (bukan antar request). Untuk detail lebih lanjut, lihat [bagian computed properties](https://livewire.laravel.com/docs/4.x/properties#computed-properties) dalam dokumentasi properties.

---

### Passing Data dari render()

Mirip dengan controller, Anda dapat passing data langsung ke view menggunakan method `render()`:

```php
public function render()
{
    return $this->view([
        'author' => Auth::user(),
        'currentTime' => now(),
    ]);
}
```

Perlu diingat bahwa `render()` berjalan pada setiap update komponen, jadi hindari operasi mahal di sini kecuali Anda membutuhkan data fresh pada setiap update.

---

## Mengorganisir Komponen

Sementara Livewire otomatis menemukan komponen di direktori `resources/views/components/` default, Anda dapat mengkustomisasi di mana Livewire mencari komponen dan mengorganisirnya menggunakan namespace.

### Namespace Komponen

Namespace komponen memungkinkan Anda mengorganisir komponen ke direktori khusus dengan syntax referensi yang clean.

Secara default, Livewire menyediakan dua namespace:

- `pages::` — Menunjuk ke `resources/views/pages/`
- `layouts::` — Menunjuk ke `resources/views/layouts/`

Anda dapat mendefinisikan namespace tambahan di file `config/livewire.php`:

```php
'component_namespaces' => [
    'layouts' => resource_path('views/layouts'),
    'pages' => resource_path('views/pages'),
    'admin' => resource_path('views/admin'),       // Namespace kustom
    'widgets' => resource_path('views/widgets'),   // Namespace kustom lainnya
],
```

Kemudian gunakan saat membuat, merender, dan routing:

```bash
php artisan make:livewire admin::users-table
```

```html
<livewire:admin::users-table />
```

```php
Route::livewire('/admin/users', 'admin::users-table');
```

---

### Lokasi Komponen Tambahan

Jika Anda ingin Livewire menemukan komponen di direktori tambahan di luar default, Anda dapat mengonfigurasinya di file `config/livewire.php`:

```php
'component_locations' => [
    resource_path('views/components'),
    resource_path('views/admin/components'),
    resource_path('views/widgets'),
],
```

Sekarang Livewire akan otomatis menemukan komponen di semua direktori ini.

---

### Registrasi Programatik

Untuk skenario lebih dinamis (seperti pengembangan package atau konfigurasi runtime), Anda dapat mendaftarkan komponen, lokasi, dan namespace secara programatik di service provider:

**Mendaftarkan komponen individual:**

```php
use Livewire\Livewire;

// Di method boot() service provider (contoh: App\Providers\AppServiceProvider)
Livewire::addComponent(
    name: 'custom-button',
    viewPath: resource_path('views/ui/button.blade.php')
);
```

**Mendaftarkan direktori komponen:**

```php
Livewire::addLocation(
    viewPath: resource_path('views/admin/components')
);
```

**Mendaftarkan namespace:**

```php
Livewire::addNamespace(
    namespace: 'ui',
    viewPath: resource_path('views/ui')
);
```

Pendekatan ini berguna saat Anda perlu mendaftarkan komponen secara kondisional atau saat membangun package Laravel yang menyediakan komponen Livewire.

---

## Komponen Berbasis Class

Untuk tim yang bermigrasi dari Livewire v3 atau mereka yang lebih suka struktur Laravel tradisional, Livewire sepenuhnya mendukung komponen berbasis class. Pendekatan ini memisahkan class PHP dan view Blade ke file berbeda di lokasi konvensional mereka.

### Membuat Komponen Berbasis Class

```bash
php artisan make:livewire CreatePost --class
```

Ini membuat dua file terpisah:

**app/Livewire/CreatePost.php**

```php
<?php

namespace App\Livewire;

use Livewire\Component;

class CreatePost extends Component
{
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

**resources/views/livewire/create-post.blade.php**

```html
<div>{{-- ... --}}</div>
```

### Kapan Menggunakan Komponen Berbasis Class

Gunakan komponen berbasis class ketika:

- Bermigrasi dari Livewire v2/v3
- Tim Anda lebih suka struktur file tradisional
- Anda memiliki konvensi yang sudah mapan seputar arsitektur berbasis class

Gunakan komponen single-file atau multi-file ketika:

- Memulai proyek Livewire v4 baru
- Anda ingin kolokasi komponen yang lebih baik
- Anda ingin menggunakan konvensi Livewire terbaru

### Mengonfigurasi Tipe Komponen Default

Jika Anda ingin komponen berbasis class secara default, konfigurasikan di `config/livewire.php`:

```php
'make_command' => [
    'type' => 'class',
],
```

---

## Mengkustomisasi Stub Komponen

Anda dapat mengkustomisasi file (atau stub) yang digunakan Livewire untuk generate komponen baru dengan menjalankan:

```bash
php artisan livewire:stubs
```

Ini membuat file stub di aplikasi Anda yang dapat Anda modifikasi:

**Stub komponen single-file:**

- `stubs/livewire-sfc.stub` — Komponen single-file

**Stub komponen multi-file:**

- `stubs/livewire-mfc-class.stub` — Class PHP untuk komponen multi-file
- `stubs/livewire-mfc-view.stub` — View Blade untuk komponen multi-file
- `stubs/livewire-mfc-js.stub` — JavaScript untuk komponen multi-file
- `stubs/livewire-mfc-test.stub` — Pest test untuk komponen multi-file

**Stub komponen berbasis class:**

- `stubs/livewire.stub` — Class PHP untuk komponen berbasis class
- `stubs/livewire.view.stub` — View Blade untuk komponen berbasis class

**Stub tambahan:**

- `stubs/livewire.attribute.stub` — Class Attribute
- `stubs/livewire.form.stub` — Class Form

Setelah dipublish, Livewire akan otomatis menggunakan stub kustom Anda saat generate komponen baru.

---

## Troubleshooting

### Komponen Tidak Ditemukan

**Gejala:** Pesan error seperti "Component [post.create] not found" atau "Unable to find component"

**Solusi:**

- Verifikasi file komponen ada di path yang diharapkan
- Periksa apakah nama komponen di view Anda cocok dengan struktur file (titik untuk subdirektori)
- Untuk komponen dengan namespace, pastikan namespace didefinisikan di `config/livewire.php` atau didaftarkan secara manual di service provider
- Coba bersihkan cache view: `php artisan view:clear`

### Komponen Menampilkan Kosong atau Tidak Merender

**Penyebab umum:**

- Elemen root hilang di template Blade Anda (Livewire memerlukan tepat satu elemen root)
- Error syntax di bagian PHP komponen Anda
- Periksa log Laravel Anda untuk pesan error detail

### Konflik Nama Class

**Gejala:** Error tentang nama class duplikat saat menggunakan komponen single-file

**Solusi:** Ini dapat terjadi jika Anda memiliki beberapa komponen single-file dengan nama yang sama di direktori berbeda. Salah satu:

- Ganti nama salah satu komponen agar unik
- Namespace salah satu direktori untuk pemisahan yang lebih jelas
