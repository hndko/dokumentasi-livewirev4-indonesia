# URL Query Parameters

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda menyinkronkan properti komponen dengan URL query parameters. Ini berguna untuk fitur seperti pencarian dan filter yang harus dapat di-bookmark dan di-share.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menginisialisasi Properti dari URL](#menginisialisasi-properti-dari-url)
- [Menggunakan Alias](#menggunakan-alias)
- [Mengecualikan Nilai Tertentu](#mengecualikan-nilai-tertentu)
- [Menampilkan pada Page Load](#menampilkan-pada-page-load)
- [Menyimpan di History](#menyimpan-di-history)
- [Menggunakan Method queryString](#menggunakan-method-querystring)
- [Trait Hooks](#trait-hooks)

---

## Penggunaan Dasar

Gunakan atribut `#[Url]` untuk menyinkronkan properti dengan URL:

```php
<?php

use Livewire\Attributes\Url;
use Livewire\Component;

new class extends Component {
    #[Url]
    public $search = '';
};
?>

<div>
    <input wire:model.live="search" type="text" placeholder="Search...">
</div>
```

Sekarang, saat pengguna mengetik di input, URL akan diperbarui menjadi:

```
https://example.com/posts?search=hello
```

---

## Menginisialisasi Properti dari URL

Properti `#[Url]` otomatis diinisialisasi dari URL saat halaman dimuat:

```php
#[Url]
public $search = '';

// Jika URL adalah ?search=hello, maka $search akan menjadi 'hello'
```

### Nullable Properties

```php
#[Url]
public ?string $search = null;
```

---

## Menggunakan Alias

Untuk menggunakan nama query parameter berbeda dari nama properti:

```php
#[Url(as: 'q')]
public $search = '';
```

URL akan menjadi `?q=hello` alih-alih `?search=hello`.

---

## Mengecualikan Nilai Tertentu

Untuk mencegah nilai tertentu muncul di URL:

```php
#[Url(except: '')]
public $search = '';
```

Ini akan menghapus parameter dari URL saat nilainya kosong.

Untuk array:

```php
#[Url(except: [])]
public $filters = [];
```

---

## Menampilkan pada Page Load

Secara default, parameter hanya ditambahkan ke URL setelah pengguna berinteraksi. Untuk menampilkan pada page load:

```php
#[Url(keep: true)]
public $search = '';
```

---

## Menyimpan di History

Secara default, perubahan URL menggunakan `replaceState`. Untuk menggunakan `pushState` (membuat entry history):

```php
#[Url(history: true)]
public $search = '';
```

---

## Menggunakan Method queryString

Alternatif untuk atribut, Anda dapat mendefinisikan method:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $search = '';
    public $page = 1;

    protected function queryString()
    {
        return [
            'search' => [
                'as' => 'q',
                'except' => '',
            ],
            'page' => [
                'except' => 1,
            ],
        ];
    }
};
```

---

## Trait Hooks

Jika menggunakan trait, Anda dapat menggunakan hook khusus:

```php
trait WithSearch
{
    #[Url]
    public $search = '';

    public function queryStringWithSearch()
    {
        return [
            'search' => [
                'as' => 'q',
            ],
        ];
    }
}
```

### Contoh Lengkap

```php
<?php

use Livewire\Attributes\Url;
use Livewire\WithPagination;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    use WithPagination;

    #[Url(except: '')]
    public $search = '';

    #[Url(except: 'all')]
    public $category = 'all';

    #[Url(as: 'sort', except: 'latest')]
    public $sortBy = 'latest';

    public function updatedSearch()
    {
        $this->resetPage();
    }

    public function updatedCategory()
    {
        $this->resetPage();
    }

    public function render()
    {
        $posts = Post::query()
            ->when($this->search, fn($q) => $q->where('title', 'like', "%{$this->search}%"))
            ->when($this->category !== 'all', fn($q) => $q->where('category', $this->category))
            ->orderBy($this->sortBy === 'latest' ? 'created_at' : 'title', 'desc')
            ->paginate(10);

        return $this->view([
            'posts' => $posts,
        ]);
    }
};
?>

<div>
    <input wire:model.live="search" type="text" placeholder="Search...">

    <select wire:model.live="category">
        <option value="all">All Categories</option>
        <option value="news">News</option>
        <option value="tutorials">Tutorials</option>
    </select>

    <select wire:model.live="sortBy">
        <option value="latest">Latest</option>
        <option value="title">Title</option>
    </select>

    @foreach ($posts as $post)
        <div wire:key="{{ $post->id }}">{{ $post->title }}</div>
    @endforeach

    {{ $posts->links() }}
</div>
```
