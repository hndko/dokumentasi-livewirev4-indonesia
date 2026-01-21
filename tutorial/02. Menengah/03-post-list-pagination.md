# Step 3: Post List dengan Pagination & Search

## 🎯 Tujuan

- Membuat komponen PostList
- Implementasi pagination
- URL query parameters untuk search & filter
- Computed properties

---

## 📝 Langkah 3.1: Generate Komponen

```bash
php artisan make:livewire PostList
```

---

## 📝 Langkah 3.2: Edit Komponen Class

Edit `app/Livewire/PostList.php`:

```php
<?php

namespace App\Livewire;

use App\Models\Post;
use Livewire\Attributes\Computed;
use Livewire\Attributes\Url;
use Livewire\Component;
use Livewire\WithPagination;

class PostList extends Component
{
    use WithPagination;

    // Sync dengan URL query parameters
    #[Url]
    public string $search = '';

    #[Url]
    public string $sortBy = 'latest';

    /**
     * Reset pagination saat search berubah
     */
    public function updatedSearch(): void
    {
        $this->resetPage();
    }

    /**
     * Reset pagination saat sort berubah
     */
    public function updatedSortBy(): void
    {
        $this->resetPage();
    }

    /**
     * Clear search
     */
    public function clearSearch(): void
    {
        $this->search = '';
        $this->resetPage();
    }

    /**
     * Computed property untuk posts
     */
    #[Computed]
    public function posts()
    {
        return Post::query()
            ->when($this->search, function ($query) {
                $query->where('title', 'like', "%{$this->search}%")
                      ->orWhere('content', 'like', "%{$this->search}%");
            })
            ->when($this->sortBy === 'latest', fn($q) => $q->latest())
            ->when($this->sortBy === 'oldest', fn($q) => $q->oldest())
            ->when($this->sortBy === 'popular', fn($q) => $q->withCount('comments')->orderByDesc('comments_count'))
            ->with(['user', 'comments'])
            ->paginate(10);
    }

    public function render()
    {
        return view('livewire.post-list');
    }
}
```

### Penjelasan Konsep:

| Konsep            | Deskripsi                           |
| ----------------- | ----------------------------------- |
| `WithPagination`  | Trait untuk pagination              |
| `#[Url]`          | Sync property dengan URL query      |
| `#[Computed]`     | Cache hasil query selama request    |
| `updatedSearch()` | Hook saat property `search` berubah |

---

## 📝 Langkah 3.3: Edit View Komponen

Edit `resources/views/livewire/post-list.blade.php`:

```html
<div>
  <!-- Search & Filter Bar -->
  <div class="bg-white rounded-lg shadow p-4 mb-6">
    <div class="flex flex-col md:flex-row gap-4">
      <!-- Search Input -->
      <div class="flex-1 relative">
        <input
          type="text"
          wire:model.live.debounce.300ms="search"
          placeholder="Cari artikel..."
          class="w-full pl-10 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
        />
        <span class="absolute left-3 top-2.5 text-gray-400">🔍</span>

        @if ($search)
        <button
          wire:click="clearSearch"
          class="absolute right-3 top-2.5 text-gray-400 hover:text-gray-600"
        >
          ✕
        </button>
        @endif
      </div>

      <!-- Sort Dropdown -->
      <select
        wire:model.live="sortBy"
        class="px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
      >
        <option value="latest">Terbaru</option>
        <option value="oldest">Terlama</option>
        <option value="popular">Terpopuler</option>
      </select>
    </div>

    <!-- Active Filters -->
    @if ($search)
    <div class="mt-3 text-sm text-gray-500">
      Hasil pencarian untuk: "<strong>{{ $search }}</strong>" ({{
      $this->posts->total() }} artikel)
    </div>
    @endif
  </div>

  <!-- Loading Indicator -->
  <div wire:loading.delay class="text-center py-8">
    <div
      class="animate-spin inline-block w-8 h-8 border-4 border-blue-500 border-t-transparent rounded-full"
    ></div>
    <p class="mt-2 text-gray-500">Memuat...</p>
  </div>

  <!-- Post Grid -->
  <div wire:loading.remove class="grid md:grid-cols-2 gap-6">
    @forelse ($this->posts as $post)
    <article
      wire:key="post-{{ $post->id }}"
      class="bg-white rounded-lg shadow overflow-hidden hover:shadow-lg transition-shadow"
    >
      <!-- Image -->
      @if ($post->image)
      <img
        src="{{ Storage::url($post->image) }}"
        alt="{{ $post->title }}"
        class="w-full h-48 object-cover"
      />
      @else
      <div
        class="w-full h-48 bg-gradient-to-br from-blue-400 to-purple-500 flex items-center justify-center"
      >
        <span class="text-6xl text-white/50">📝</span>
      </div>
      @endif

      <!-- Content -->
      <div class="p-5">
        <h2 class="text-xl font-bold mb-2 line-clamp-2">
          <a
            href="/posts/{{ $post->slug }}"
            wire:navigate
            class="hover:text-blue-600 transition-colors"
          >
            {{ $post->title }}
          </a>
        </h2>

        <p class="text-gray-600 mb-4 line-clamp-3">
          {{ Str::limit($post->content, 150) }}
        </p>

        <!-- Meta -->
        <div class="flex items-center justify-between text-sm text-gray-500">
          <div class="flex items-center gap-2">
            <span
              class="w-6 h-6 bg-gray-200 rounded-full flex items-center justify-center text-xs"
            >
              {{ substr($post->user->name, 0, 1) }}
            </span>
            <span>{{ $post->user->name }}</span>
          </div>
          <div class="flex items-center gap-3">
            <span>💬 {{ $post->comments->count() }}</span>
            <span>{{ $post->created_at->diffForHumans() }}</span>
          </div>
        </div>
      </div>
    </article>
    @empty
    <div class="col-span-2 text-center py-12 bg-white rounded-lg">
      <div class="text-6xl mb-4">🔍</div>
      <h3 class="text-xl font-semibold mb-2">Tidak ada artikel</h3>
      <p class="text-gray-500">
        @if ($search) Coba kata kunci lain @else Belum ada artikel yang
        dipublikasikan @endif
      </p>
    </div>
    @endforelse
  </div>

  <!-- Pagination -->
  @if ($this->posts->hasPages())
  <div class="mt-8">{{ $this->posts->links() }}</div>
  @endif
</div>
```

---

## 📝 Langkah 3.4: Setup Route

Edit `routes/web.php`:

```php
<?php

use App\Livewire\PostList;
use Illuminate\Support\Facades\Route;

Route::get('/', PostList::class)->name('home');
Route::get('/posts', PostList::class)->name('posts.index');
```

---

## 📝 Langkah 3.5: Buat Layout

Buat `resources/views/components/layouts/app.blade.php`:

```html
<!DOCTYPE html>
<html lang="id">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{ $title ?? 'Blog App' }}</title>
    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body class="bg-gray-100 min-h-screen">
    <!-- Header -->
    <header class="bg-white shadow">
      <div
        class="max-w-6xl mx-auto px-4 py-4 flex justify-between items-center"
      >
        <a href="/" wire:navigate class="text-xl font-bold text-gray-800">
          📚 Blog
        </a>
        <nav class="flex gap-4">
          <a
            href="/posts"
            wire:navigate
            class="text-gray-600 hover:text-gray-900"
            >Artikel</a
          >
          @auth
          <a
            href="/posts/create"
            wire:navigate
            class="text-blue-600 hover:text-blue-800"
            >+ Tulis</a
          >
          @else
          <a href="/login" class="text-gray-600 hover:text-gray-900">Login</a>
          @endauth
        </nav>
      </div>
    </header>

    <!-- Main -->
    <main class="max-w-6xl mx-auto px-4 py-8">{{ $slot }}</main>
  </body>
</html>
```

---

## 📝 Langkah 3.6: Test di Browser

```bash
php artisan serve
```

Buka `http://localhost:8000` dan test:

- Search dengan keyword
- Ubah sorting
- Navigasi pagination
- Perhatikan URL berubah

---

## ✅ Checkpoint

- [x] PostList dengan pagination
- [x] Search dengan debounce
- [x] Sort by latest/oldest/popular
- [x] URL sync dengan #[Url]
- [x] Computed properties

---

## ⬅️ [Step 2: Models](02-models-relationships.md) | ➡️ [Step 4: Create Post](04-create-post-upload.md)
