# Step 5: Show Post & Nested Comments

## 🎯 Tujuan

- Membuat halaman detail post
- Komponen nested untuk comments
- Lazy loading untuk comments

---

## 📝 Langkah 5.1: Generate Komponen ShowPost

```bash
php artisan make:livewire ShowPost
```

---

## 📝 Langkah 5.2: Edit ShowPost Component

Edit `app/Livewire/ShowPost.php`:

```php
<?php

namespace App\Livewire;

use App\Models\Post;
use Livewire\Attributes\Layout;
use Livewire\Attributes\Title;
use Livewire\Component;

class ShowPost extends Component
{
    public Post $post;

    /**
     * Mount dengan slug
     */
    public function mount(string $slug)
    {
        $this->post = Post::where('slug', $slug)
            ->with('user')
            ->firstOrFail();
    }

    #[Layout('components.layouts.app')]
    public function render()
    {
        return view('livewire.show-post')
            ->title($this->post->title);
    }
}
```

---

## 📝 Langkah 5.3: Edit ShowPost View

Edit `resources/views/livewire/show-post.blade.php`:

```html
<article class="max-w-4xl mx-auto">
  <!-- Flash Message -->
  @if (session('success'))
  <div class="bg-green-100 text-green-700 px-4 py-3 rounded-lg mb-6">
    {{ session('success') }}
  </div>
  @endif

  <!-- Post Card -->
  <div class="bg-white rounded-lg shadow-lg overflow-hidden">
    <!-- Cover Image -->
    @if ($post->image)
    <img
      src="{{ Storage::url($post->image) }}"
      alt="{{ $post->title }}"
      class="w-full h-64 md:h-96 object-cover"
    />
    @endif

    <!-- Content -->
    <div class="p-6 md:p-8">
      <!-- Title -->
      <h1 class="text-3xl md:text-4xl font-bold text-gray-900 mb-4">
        {{ $post->title }}
      </h1>

      <!-- Meta -->
      <div
        class="flex items-center gap-4 text-sm text-gray-500 mb-8 pb-8 border-b"
      >
        <div class="flex items-center gap-2">
          <span
            class="w-10 h-10 bg-blue-500 text-white rounded-full flex items-center justify-center font-bold"
          >
            {{ substr($post->user->name, 0, 1) }}
          </span>
          <div>
            <p class="font-medium text-gray-900">{{ $post->user->name }}</p>
            <p>{{ $post->created_at->format('d M Y, H:i') }}</p>
          </div>
        </div>
      </div>

      <!-- Body -->
      <div class="prose prose-lg max-w-none">
        {!! nl2br(e($post->content)) !!}
      </div>
    </div>
  </div>

  <!-- Comments Section (Lazy Loaded) -->
  <div class="mt-8">
    <livewire:comment-section :post="$post" lazy />
  </div>

  <!-- Back Link -->
  <div class="mt-8 text-center">
    <a href="/posts" wire:navigate class="text-blue-600 hover:underline">
      ← Kembali ke daftar artikel
    </a>
  </div>
</article>
```

---

## 📝 Langkah 5.4: Generate Komponen CommentSection

```bash
php artisan make:livewire CommentSection
```

---

## 📝 Langkah 5.5: Edit CommentSection Component

Edit `app/Livewire/CommentSection.php`:

```php
<?php

namespace App\Livewire;

use App\Models\Comment;
use App\Models\Post;
use Livewire\Attributes\Locked;
use Livewire\Attributes\On;
use Livewire\Attributes\Validate;
use Livewire\Component;

class CommentSection extends Component
{
    #[Locked]
    public Post $post;

    #[Validate('required|min:3|max:1000')]
    public string $body = '';

    public bool $isReplying = true;

    /**
     * Placeholder untuk lazy loading
     */
    public static function placeholder()
    {
        return <<<'HTML'
        <div class="bg-white rounded-lg shadow p-6">
            <div class="animate-pulse">
                <div class="h-6 bg-gray-200 rounded w-1/3 mb-4"></div>
                <div class="h-24 bg-gray-200 rounded mb-4"></div>
                <div class="space-y-4">
                    <div class="h-20 bg-gray-200 rounded"></div>
                    <div class="h-20 bg-gray-200 rounded"></div>
                </div>
            </div>
        </div>
        HTML;
    }

    /**
     * Tambah komentar baru
     */
    public function addComment()
    {
        if (!auth()->check()) {
            return redirect()->route('login');
        }

        $this->validate();

        $this->post->comments()->create([
            'body' => $this->body,
            'user_id' => auth()->id(),
        ]);

        $this->body = '';

        // Dispatch event untuk komponen lain
        $this->dispatch('comment-added');
    }

    /**
     * Hapus komentar
     */
    public function deleteComment(int $commentId)
    {
        $comment = Comment::find($commentId);

        // Hanya pemilik yang bisa hapus
        if ($comment && $comment->user_id === auth()->id()) {
            $comment->delete();
        }
    }

    /**
     * Refresh saat ada komentar baru (dari komponen lain)
     */
    #[On('comment-added')]
    public function refreshComments()
    {
        // Method kosong, Livewire otomatis render ulang
    }

    public function render()
    {
        return view('livewire.comment-section', [
            'comments' => $this->post->comments()
                ->with('user')
                ->latest()
                ->get(),
        ]);
    }
}
```

---

## 📝 Langkah 5.6: Edit CommentSection View

Edit `resources/views/livewire/comment-section.blade.php`:

```html
<div class="bg-white rounded-lg shadow-lg p-6">
  <!-- Header -->
  <h3 class="text-xl font-bold mb-6 flex items-center gap-2">
    💬 Komentar
    <span class="text-sm font-normal text-gray-500"
      >({{ $comments->count() }})</span
    >
  </h3>

  <!-- Form Komentar -->
  @auth
  <form wire:submit="addComment" class="mb-8">
    <textarea
      wire:model="body"
      rows="3"
      placeholder="Tulis komentar Anda..."
      class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent resize-none"
    ></textarea>

    @error('body')
    <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
    @enderror

    <div class="flex justify-end mt-3">
      <button
        type="submit"
        class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors disabled:opacity-50"
        wire:loading.attr="disabled"
        wire:target="addComment"
      >
        <span wire:loading.remove wire:target="addComment">Kirim Komentar</span>
        <span wire:loading wire:target="addComment">Mengirim...</span>
      </button>
    </div>
  </form>
  @else
  <div class="mb-8 p-4 bg-gray-50 rounded-lg text-center">
    <p class="text-gray-600">
      <a href="/login" class="text-blue-600 hover:underline">Login</a>
      untuk mengirim komentar
    </p>
  </div>
  @endauth

  <!-- Daftar Komentar -->
  <div class="space-y-4">
    @forelse ($comments as $comment)
    <div
      wire:key="comment-{{ $comment->id }}"
      class="p-4 bg-gray-50 rounded-lg"
    >
      <div class="flex justify-between items-start mb-2">
        <div class="flex items-center gap-2">
          <span
            class="w-8 h-8 bg-blue-500 text-white rounded-full flex items-center justify-center text-sm font-bold"
          >
            {{ substr($comment->user->name, 0, 1) }}
          </span>
          <div>
            <p class="font-medium text-gray-900">{{ $comment->user->name }}</p>
            <p class="text-xs text-gray-500">
              {{ $comment->created_at->diffForHumans() }}
            </p>
          </div>
        </div>

        @if (auth()->id() === $comment->user_id)
        <button
          wire:click="deleteComment({{ $comment->id }})"
          wire:confirm="Hapus komentar ini?"
          class="text-red-500 hover:text-red-700 text-sm"
        >
          Hapus
        </button>
        @endif
      </div>

      <p class="text-gray-700 pl-10">{{ $comment->body }}</p>
    </div>
    @empty
    <div class="text-center py-8 text-gray-500">
      <div class="text-4xl mb-2">💭</div>
      <p>Belum ada komentar. Jadilah yang pertama!</p>
    </div>
    @endforelse
  </div>
</div>
```

---

## 📝 Langkah 5.7: Tambahkan Route

Edit `routes/web.php`:

```php
use App\Livewire\ShowPost;

Route::get('/posts/{slug}', ShowPost::class)->name('posts.show');
```

---

## 📝 Langkah 5.8: Test

1. Buka salah satu post dari daftar
2. Perhatikan lazy loading untuk comments
3. Tambahkan komentar (perlu login)
4. Test hapus komentar

---

## ✅ Checkpoint

- [x] Halaman detail post
- [x] Nested component untuk comments
- [x] Lazy loading dengan placeholder
- [x] Add/delete comments
- [x] Hanya pemilik bisa hapus

---

## ⬅️ [Step 4: Create Post](04-create-post-upload.md) | ➡️ [Step 6: Events](06-events-realtime.md)
