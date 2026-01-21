# Forms

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Karena form adalah tulang punggung sebagian besar aplikasi web, Livewire menyediakan banyak utilitas berguna untuk membangunnya. Dari menangani elemen input sederhana hingga hal-hal kompleks seperti validasi real-time atau upload file, Livewire memiliki alat sederhana dan terdokumentasi dengan baik untuk membuat hidup Anda lebih mudah dan menyenangkan pengguna Anda.

Mari kita mulai.

---

## Daftar Isi

- [Mengirim Form](#mengirim-form)
- [Menambahkan Validasi](#menambahkan-validasi)
- [Menampilkan Indikator Loading](#menampilkan-indikator-loading)
- [Field yang Live-updating](#field-yang-live-updating)
- [Mengupdate Field Hanya pada Blur](#mengupdate-field-hanya-pada-blur)
- [Validasi Real-time](#validasi-real-time)

---

## Mengirim Form

Mari kita mulai dengan melihat form yang sangat sederhana dalam komponen `post.create`. Form ini akan memiliki dua input teks sederhana dan tombol submit, serta beberapa kode di backend untuk mengelola state dan pengiriman form:

```php
<?php
// resources/views/components/post/⚡create.blade.php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public $title = '';
    public $content = '';

    public function save()
    {
        Post::create(
            $this->only(['title', 'content'])
        );

        session()->flash('status', 'Post berhasil diupdate.');

        return $this->redirect('/posts');
    }
};
?>

<form wire:submit="save">
    <input type="text" wire:model="title">
    <input type="text" wire:model="content">
    <button type="submit">Save</button>
</form>
```

Seperti yang Anda lihat, kita "binding" properti public `$title` dan `$content` di form di atas menggunakan `wire:model`. Ini adalah salah satu fitur paling umum digunakan dan powerful dari Livewire.

Selain binding `$title` dan `$content`, kita menggunakan `wire:submit` untuk menangkap event submit saat tombol "Save" diklik dan memanggil aksi `save()`. Aksi ini akan mempersist input form ke database.

Setelah post baru dibuat di database, kita redirect pengguna ke halaman posts dan menampilkan pesan "flash" bahwa post baru telah dibuat.

---

## Menambahkan Validasi

Untuk menghindari menyimpan input pengguna yang tidak lengkap atau berbahaya, sebagian besar form membutuhkan validasi input.

Livewire membuat validasi form Anda sesederhana menambahkan atribut `#[Validate]` di atas properti yang ingin divalidasi.

Setelah properti memiliki atribut `#[Validate]` yang dilampirkan, aturan validasi akan diterapkan ke nilai properti setiap kali diupdate di sisi server.

Mari tambahkan beberapa aturan validasi dasar ke properti `$title` dan `$content`:

```php
<?php

use Livewire\Attributes\Validate;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    #[Validate('required')]
    public $title = '';

    #[Validate('required')]
    public $content = '';

    public function save()
    {
        $this->validate();

        Post::create(
            $this->only(['title', 'content'])
        );

        return $this->redirect('/posts');
    }
};
```

Kita juga akan memodifikasi template Blade untuk menampilkan error validasi:

```html
<form wire:submit="save">
  <input type="text" wire:model="title" />
  <div>
    @error('title')
    <span class="error">{{ $message }}</span>
    @enderror
  </div>

  <input type="text" wire:model="content" />
  <div>
    @error('content')
    <span class="error">{{ $message }}</span>
    @enderror
  </div>

  <button type="submit">Save</button>
</form>
```

Sekarang, jika pengguna mencoba mengirim form tanpa mengisi field manapun, mereka akan melihat pesan validasi yang memberitahu field mana yang wajib diisi sebelum menyimpan post.

Livewire memiliki lebih banyak fitur validasi. Untuk informasi lebih lanjut, kunjungi [halaman dokumentasi khusus tentang Validasi](https://livewire.laravel.com/docs/4.x/validation).

---

## Menampilkan Indikator Loading

Secara default, Livewire akan otomatis menonaktifkan tombol submit dan menandai input sebagai `readonly` saat form sedang dikirim, mencegah pengguna mengirim form lagi saat pengiriman pertama sedang ditangani.

Namun, bisa sulit bagi pengguna untuk mendeteksi state "loading" ini tanpa affordance tambahan di UI aplikasi Anda.

Berikut contoh menambahkan spinner loading kecil ke tombol "Save" via `wire:loading`:

```html
<button type="submit">
  Save
  <div wire:loading>
    <svg>...</svg>
    <!-- SVG loading spinner -->
  </div>
</button>
```

Alternatifnya, Anda dapat menggunakan Tailwind dan atribut `data-loading` otomatis Livewire:

```html
<button type="submit">
  <span class="in-data-loading:hidden">Save</span>
  <span class="not-in-data-loading:hidden">
    <svg>...</svg>
    <!-- SVG loading spinner -->
  </span>
</button>
```

---

## Field yang Live-updating

Secara default, Livewire hanya mengirim network request saat form dikirim (atau aksi lain dipanggil), bukan saat form sedang diisi.

Jika Anda ingin memastikan field input "title" disinkronkan dengan properti `$title` di backend saat pengguna mengetik, Anda dapat menambahkan modifier `.live` ke `wire:model`:

```html
<input type="text" wire:model.live="title" />
```

Sekarang, saat pengguna mengetik di field ini, network request akan dikirim ke server untuk mengupdate `$title`. Ini berguna untuk hal-hal seperti pencarian real-time, di mana dataset difilter saat pengguna mengetik.

---

## Mengupdate Field Hanya pada Blur

Untuk sebagian besar kasus, `wire:model.live` baik-baik saja untuk updating field form real-time; namun, bisa terlalu intensif pada resource jaringan untuk input teks.

Jika alih-alih mengirim network request saat pengguna mengetik, Anda ingin hanya mengirim request saat pengguna "tab" keluar dari input teks (juga disebut "blur" input), Anda dapat menggunakan modifier `.blur`:

```html
<input type="text" wire:model.blur="title" />
```

Sekarang class komponen di server tidak akan diupdate sampai pengguna menekan tab atau mengklik di luar input teks.

---

## Validasi Real-time

Terkadang, Anda mungkin ingin menampilkan error validasi saat pengguna mengisi form. Dengan cara ini, mereka diperingatkan lebih awal bahwa ada yang salah alih-alih harus menunggu sampai seluruh form diisi.

Livewire menangani hal ini secara otomatis. Dengan menggunakan `.live` atau `.blur` pada `wire:model`, Livewire akan mengirim network request saat pengguna mengisi form. Setiap request tersebut akan menjalankan aturan validasi yang sesuai sebelum mengupdate setiap properti. Jika validasi gagal, properti tidak akan diupdate di server dan pesan validasi akan ditampilkan kepada pengguna:

```html
<input type="text" wire:model.blur="title" />
<div>
  @error('title')
  <span class="error">{{ $message }}</span>
  @enderror
</div>
```

```php
#[Validate('required|min:5')]
public $title = '';
```

Sekarang, jika pengguna hanya mengetik tiga karakter ke input "title", kemudian mengklik input berikutnya di form, pesan validasi akan ditampilkan yang menunjukkan ada minimum lima karakter untuk field tersebut.

Untuk informasi lebih lanjut, lihat [halaman dokumentasi validasi](https://livewire.laravel.com/docs/4.x/validation).
