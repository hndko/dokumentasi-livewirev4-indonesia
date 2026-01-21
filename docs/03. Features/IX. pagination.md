# Pagination

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire menyediakan integrasi paginasi yang seamless dengan Laravel. Anda dapat menggunakan paginasi Laravel biasa dengan manfaat tambahan update real-time tanpa reload halaman.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menonaktifkan URL Query String](#menonaktifkan-url-query-string)
- [Menyesuaikan Perilaku Scroll](#menyesuaikan-perilaku-scroll)
- [Mereset Halaman](#mereset-halaman)
- [Multiple Paginators](#multiple-paginators)
- [Hooking ke Page Updates](#hooking-ke-page-updates)
- [Tema Paginasi](#tema-paginasi)
- [View Paginasi Kustom](#view-paginasi-kustom)

---

## Penggunaan Dasar

Gunakan trait `WithPagination`:

```php
<?php

use Livewire\WithPagination;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    use WithPagination;

    public function render()
    {
        return $this->view([
            'posts' => Post::paginate(10),
        ]);
    }
};
?>

<div>
    @foreach ($posts as $post)
        <div wire:key="{{ $post->id }}">
            {{ $post->title }}
        </div>
    @endforeach

    {{ $posts->links() }}
</div>
```

---

## Menonaktifkan URL Query String

Secara default, Livewire memperbarui URL dengan parameter halaman. Untuk menonaktifkan:

```php
use Livewire\WithPagination;

new class extends Component {
    use WithPagination;

    protected $paginationUrl = false;
};
```

---

## Menyesuaikan Perilaku Scroll

Secara default, Livewire scroll ke atas saat halaman berubah. Untuk menonaktifkan:

```php
use Livewire\WithPagination;

new class extends Component {
    use WithPagination;

    protected $paginateScroll = false;
};
```

---

## Mereset Halaman

Reset ke halaman pertama saat filter berubah:

```php
public $search = '';

public function updatedSearch()
{
    $this->resetPage();
}
```

### Method Navigasi Halaman yang Tersedia

```php
// Reset ke halaman pertama
$this->resetPage();

// Pergi ke halaman tertentu
$this->setPage(5);

// Halaman berikutnya
$this->nextPage();

// Halaman sebelumnya
$this->previousPage();
```

---

## Multiple Paginators

```php
<?php

use Livewire\WithPagination;
use Livewire\Component;

new class extends Component {
    use WithPagination;

    public function render()
    {
        return $this->view([
            'posts' => Post::paginate(10, pageName: 'posts'),
            'users' => User::paginate(10, pageName: 'users'),
        ]);
    }
};
?>

<div>
    @foreach ($posts as $post)
        <!-- ... -->
    @endforeach
    {{ $posts->links() }}

    @foreach ($users as $user)
        <!-- ... -->
    @endforeach
    {{ $users->links() }}
</div>
```

Reset paginator tertentu:

```php
$this->resetPage('users');
```

---

## Hooking ke Page Updates

### Named Paginator Hooks

```php
public function updatingPostsPage($page)
{
    // Sebelum halaman posts diupdate
}

public function updatedPostsPage($page)
{
    // Setelah halaman posts diupdate
}
```

### General Paginator Hooks

```php
public function updatingPage($page)
{
    // Sebelum halaman apapun diupdate
}

public function updatedPage($page)
{
    // Setelah halaman apapun diupdate
}
```

---

## Tema Paginasi

### Simple Theme

```php
Post::simplePaginate(10);
```

### Cursor Pagination

```php
Post::cursorPaginate(10);
```

### Bootstrap Alih-alih Tailwind

```php
use Livewire\WithPagination;

new class extends Component {
    use WithPagination;

    protected $paginationTheme = 'bootstrap';
};
```

---

## View Paginasi Kustom

### Via ->links()

```html
{{ $posts->links('custom-pagination-view') }}
```

### Via paginationView()

```php
public function paginationView()
{
    return 'custom-pagination-view';
}

public function paginationSimpleView()
{
    return 'custom-simple-pagination-view';
}
```

### Mempublikasikan View Default

```bash
php artisan vendor:publish --tag=livewire:pagination
```

### Contoh View Paginasi

```html
<!-- resources/views/custom-pagination-view.blade.php -->
<nav>
  @if ($paginator->hasPages())
  <ul>
    {{-- Previous --}} @if ($paginator->onFirstPage())
    <li><span>Previous</span></li>
    @else
    <li>
      <button wire:click="previousPage" wire:loading.attr="disabled">
        Previous
      </button>
    </li>
    @endif {{-- Page Numbers --}} @foreach ($elements as $element) @if
    (is_array($element)) @foreach ($element as $page => $url) @if ($page ==
    $paginator->currentPage())
    <li><span>{{ $page }}</span></li>
    @else
    <li>
      <button wire:click="gotoPage({{ $page }})">{{ $page }}</button>
    </li>
    @endif @endforeach @endif @endforeach {{-- Next --}} @if
    ($paginator->hasMorePages())
    <li>
      <button wire:click="nextPage" wire:loading.attr="disabled">Next</button>
    </li>
    @else
    <li><span>Next</span></li>
    @endif
  </ul>
  @endif
</nav>
```
