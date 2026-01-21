# Computed Properties

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Computed properties adalah method di komponen Anda yang ditandai dengan atribut `#[Computed]`. Mereka dapat diakses sebagai properti dinamis yang tidak disimpan sebagai bagian dari state komponen tetapi dievaluasi secara on-the-fly.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Keuntungan Performa](#keuntungan-performa)
- [Caching Antar Request](#caching-antar-request)
- [Caching di Semua Komponen](#caching-di-semua-komponen)
- [Kapan Menggunakan Computed Properties](#kapan-menggunakan-computed-properties)
- [Alternatif: Session Properties](#alternatif-session-properties)

---

## Penggunaan Dasar

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    #[Computed]
    public function posts()
    {
        return Post::all();
    }
};
?>

<div>
    @foreach ($this->posts as $post)
        <div wire:key="{{ $post->id }}">{{ $post->title }}</div>
    @endforeach
</div>
```

> **Penting:** Akses computed property dengan `$this->posts`, bukan `$posts`.

---

## Keuntungan Performa

Computed properties otomatis di-memoize setelah penggunaan pertama selama satu request. Ini berarti Anda dapat dengan bebas mengakses `$this->posts` dalam komponen dan yakin bahwa method sebenarnya hanya akan dipanggil sekali.

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function expensiveCalculation()
    {
        // Query mahal ini hanya dijalankan sekali per request
        return SomeModel::complexQuery()->get();
    }

    public function someAction()
    {
        // Akses pertama - query dijalankan
        $data = $this->expensiveCalculation;

        // Akses kedua - hasil cached digunakan
        $sameData = $this->expensiveCalculation;
    }
};
```

### Menghapus Memo

Untuk menghapus cache dan mengeksekusi ulang computed property:

```php
unset($this->posts);
```

---

## Caching Antar Request

Untuk cache hasil antar request (menggunakan Laravel Cache):

```php
#[Computed(persist: true)]
public function posts()
{
    return Post::all();
}
```

Dengan durasi custom (dalam detik):

```php
#[Computed(persist: true, seconds: 3600)]
public function posts()
{
    return Post::all();
}
```

---

## Caching di Semua Komponen

Untuk cache hasil di semua instance komponen:

```php
#[Computed(cache: true)]
public function globalSettings()
{
    return Settings::all();
}
```

Dengan key custom:

```php
#[Computed(cache: true, key: 'global-settings')]
public function globalSettings()
{
    return Settings::all();
}
```

---

## Kapan Menggunakan Computed Properties

### Mengakses Nilai Secara Kondisional

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    public $showPosts = false;

    #[Computed]
    public function posts()
    {
        // Hanya dijalankan jika $showPosts true
        return Post::all();
    }
};
?>

<div>
    @if ($showPosts)
        @foreach ($this->posts as $post)
            <!-- ... -->
        @endforeach
    @endif
</div>
```

### Menggunakan Inline Templates

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function posts()
    {
        return Post::all();
    }
};
?>

<div>
    @foreach ($this->posts as $post)
        <div>{{ $post->title }}</div>
    @endforeach
</div>
```

### Menghilangkan Method render

Dengan computed properties, Anda dapat menghilangkan method `render()`:

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function posts()
    {
        return Post::all();
    }

    // Tidak perlu method render()
};
?>

<div>
    @foreach ($this->posts as $post)
        <div>{{ $post->title }}</div>
    @endforeach
</div>
```

---

## Alternatif: Session Properties

Untuk menyimpan nilai di session alih-alih state komponen:

```php
use Livewire\Attributes\Session;

#[Session]
public $filter = 'all';
```

Nilai akan dipertahankan antar request dan antar halaman.

### Contoh Lengkap

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Attributes\Url;
use Livewire\WithPagination;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    use WithPagination;

    #[Url]
    public $search = '';

    #[Computed]
    public function posts()
    {
        return Post::query()
            ->when($this->search, fn($q) => $q->where('title', 'like', "%{$this->search}%"))
            ->latest()
            ->paginate(10);
    }

    #[Computed(persist: true, seconds: 60)]
    public function categories()
    {
        return Category::all();
    }

    #[Computed(cache: true)]
    public function siteSettings()
    {
        return Settings::all();
    }
};
?>

<div>
    <input wire:model.live="search" type="text">

    <select wire:model.live="category">
        @foreach ($this->categories as $category)
            <option value="{{ $category->id }}">{{ $category->name }}</option>
        @endforeach
    </select>

    @foreach ($this->posts as $post)
        <div wire:key="{{ $post->id }}">{{ $post->title }}</div>
    @endforeach

    {{ $this->posts->links() }}
</div>
```
