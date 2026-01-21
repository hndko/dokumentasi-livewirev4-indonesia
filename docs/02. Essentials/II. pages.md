# Pages

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Komponen Livewire dapat digunakan sebagai halaman penuh dengan menetapkannya langsung ke route. Ini memungkinkan Anda membangun halaman aplikasi lengkap menggunakan komponen Livewire, dengan kemampuan tambahan seperti layout kustom, judul halaman, dan parameter route.

---

## Daftar Isi

- [Routing ke Komponen](#routing-ke-komponen)
- [Layouts](#layouts)
  - [Layout Khusus Komponen](#layout-khusus-komponen)
- [Menyetel Judul Halaman](#menyetel-judul-halaman)
- [Menyetel Slot File Layout Tambahan](#menyetel-slot-file-layout-tambahan)
- [Mengakses Parameter Route](#mengakses-parameter-route)
- [Menggunakan Route Model Binding](#menggunakan-route-model-binding)
- [Lihat Juga](#lihat-juga)

---

## Routing ke Komponen

Untuk route ke komponen, gunakan method `Route::livewire()` di file `routes/web.php` Anda:

```php
Route::livewire('/posts/create', 'pages::post.create');
```

Saat Anda mengunjungi URL yang ditentukan, komponen akan di-render sebagai halaman lengkap menggunakan layout aplikasi Anda.

---

## Layouts

Komponen yang di-render via route akan menggunakan file layout aplikasi Anda. Secara default, Livewire mencari layout bernama `layouts::app` yang terletak di `resources/views/layouts/app.blade.php`.

Anda dapat membuat file ini jika belum ada dengan menjalankan perintah berikut:

```bash
php artisan livewire:layout
```

Perintah ini akan menghasilkan file bernama `resources/views/layouts/app.blade.php`.

Pastikan Anda telah membuat file Blade di lokasi ini dan menyertakan placeholder `{{ $slot }}`:

```html
<!-- resources/views/layouts/app.blade.php -->
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

Anda dapat mengkustomisasi layout default dengan mengupdate opsi konfigurasi `component_layout` di file `config/livewire.php`:

```php
'component_layout' => 'layouts::dashboard',
```

---

### Layout Khusus Komponen

Untuk menggunakan layout berbeda untuk komponen tertentu, Anda dapat menempatkan atribut `#[Layout]` di atas class komponen:

```php
<?php

use Livewire\Attributes\Layout;
use Livewire\Component;

new #[Layout('layouts::dashboard')] class extends Component {
    // ...
};
```

Alternatifnya, Anda dapat menggunakan method `->layout()` dalam method `render()` komponen:

```php
<?php

use Livewire\Component;

new class extends Component {
    // ...

    public function render()
    {
        return $this->view()
            ->layout('layouts::dashboard');
    }
};
```

---

## Menyetel Judul Halaman

Menetapkan judul halaman unik untuk setiap halaman di aplikasi Anda berguna untuk pengguna dan mesin pencari.

Untuk menyetel judul halaman kustom untuk komponen halaman, pertama, pastikan file layout Anda menyertakan judul dinamis:

```html
<head>
  <title>{{ $title ?? config('app.name') }}</title>
</head>
```

Selanjutnya, di atas class komponen Livewire, tambahkan atribut `#[Title]` dan berikan judul halaman Anda:

```php
<?php

use Livewire\Attributes\Title;
use Livewire\Component;

new #[Title('Buat Post')] class extends Component {
    // ...
};
```

Ini akan menyetel judul halaman untuk komponen. Dalam contoh ini, judul halaman akan menjadi "Buat Post" saat komponen di-render.

Jika Anda perlu passing judul dinamis, seperti judul yang menggunakan properti komponen, Anda dapat menggunakan method fluent `->title()` dalam method `render()` komponen:

```php
public function render()
{
    return $this->view()
        ->title('Buat Post');
}
```

---

## Menyetel Slot File Layout Tambahan

Jika file layout Anda memiliki named slot selain `$slot`, Anda dapat menyetel kontennya di view Blade Anda dengan mendefinisikan `<x-slot>` di luar elemen root. Misalnya, jika Anda ingin dapat menyetel bahasa halaman untuk setiap komponen secara individual, Anda dapat menambahkan slot `$lang` dinamis ke tag HTML pembuka di file layout:

```html
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', $lang ?? app()->getLocale()) }}">
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

Kemudian, di view komponen, definisikan elemen `<x-slot>` di luar elemen root:

```html
<x-slot:lang>id</x-slot> {{-- Komponen ini dalam Bahasa Indonesia --}}

<div>
    {{-- Konten Indonesia... --}}
</div>
```

---

## Mengakses Parameter Route

Saat bekerja dengan komponen halaman, Anda mungkin perlu mengakses parameter route dalam komponen Livewire Anda.

Untuk mendemonstrasikan, pertama, definisikan route dengan parameter di file `routes/web.php`:

```php
Route::livewire('/posts/{id}', 'pages::show-post');
```

Di sini, kita telah mendefinisikan route dengan parameter `id` yang merepresentasikan ID post.

Selanjutnya, update komponen Livewire Anda untuk menerima parameter route di method `mount()`:

```php
<?php

use App\Models\Post;
use Livewire\Component;

new class extends Component {
    public Post $post;

    public function mount($id)
    {
        $this->post = Post::findOrFail($id);
    }
};
```

Dalam contoh ini, karena nama parameter `$id` cocok dengan parameter route `{id}`, jika URL `/posts/1` dikunjungi, Livewire akan passing nilai "1" sebagai `$id`.

---

## Menggunakan Route Model Binding

Route model binding Laravel memungkinkan Anda untuk otomatis resolve model Eloquent dari parameter route.

Setelah mendefinisikan route dengan parameter model di file `routes/web.php`:

```php
Route::livewire('/posts/{post}', 'pages::show-post');
```

Anda sekarang dapat menerima parameter route model melalui method `mount()` komponen:

```php
<?php

use App\Models\Post;
use Livewire\Component;

new class extends Component {
    public Post $post;

    public function mount(Post $post)
    {
        $this->post = $post;
    }
};
```

Livewire tahu untuk menggunakan "route model binding" karena type-hint `Post` ditambahkan sebelum parameter `$post` di `mount()`.

Seperti sebelumnya, Anda dapat mengurangi boilerplate dengan menghilangkan method `mount()`:

```php
<?php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public Post $post;
};
```

Properti `$post` akan otomatis ditetapkan ke model yang di-bind via parameter route `{post}`.

---

## Lihat Juga

- [Components](./IV.%20components.md) — Pelajari tentang membuat dan mengorganisir komponen
- [Navigate](https://livewire.laravel.com/docs/4.x/navigate) — Bangun navigasi seperti SPA antar halaman
- [Redirecting](https://livewire.laravel.com/docs/4.x/redirecting) — Redirect pengguna setelah form submission atau aksi
- [Layout Attribute](https://livewire.laravel.com/docs/4.x/attribute-layout) — Tentukan layout untuk komponen halaman penuh
- [Title Attribute](https://livewire.laravel.com/docs/4.x/attribute-title) — Setel judul halaman secara dinamis
