# Step 4: Create Post dengan File Upload

## 🎯 Tujuan

- Membuat form create post
- Implementasi file upload
- Preview gambar sebelum upload
- Progress indicator

---

## 📝 Langkah 4.1: Generate Komponen

```bash
php artisan make:livewire CreatePost
```

---

## 📝 Langkah 4.2: Edit Komponen Class

Edit `app/Livewire/CreatePost.php`:

```php
<?php

namespace App\Livewire;

use App\Models\Post;
use Illuminate\Support\Str;
use Livewire\Attributes\Layout;
use Livewire\Attributes\Title;
use Livewire\Attributes\Validate;
use Livewire\Component;
use Livewire\WithFileUploads;

#[Layout('components.layouts.app')]
#[Title('Tulis Artikel Baru')]
class CreatePost extends Component
{
    use WithFileUploads;

    #[Validate('required|min:5|max:255')]
    public string $title = '';

    #[Validate('required|min:50')]
    public string $content = '';

    #[Validate('nullable|image|max:2048')] // Max 2MB
    public $image;

    /**
     * Submit form dan simpan post
     */
    public function save()
    {
        // Harus login
        if (!auth()->check()) {
            return redirect()->route('login');
        }

        // Validasi
        $this->validate();

        // Upload gambar jika ada
        $imagePath = null;
        if ($this->image) {
            $imagePath = $this->image->store('posts', 'public');
        }

        // Simpan ke database
        $post = Post::create([
            'title' => $this->title,
            'slug' => Str::slug($this->title) . '-' . uniqid(),
            'content' => $this->content,
            'image' => $imagePath,
            'user_id' => auth()->id(),
        ]);

        // Redirect ke post detail
        session()->flash('success', 'Artikel berhasil dipublikasikan!');

        return $this->redirect("/posts/{$post->slug}", navigate: true);
    }

    /**
     * Hapus gambar yang sudah diupload
     */
    public function removeImage()
    {
        $this->image = null;
    }

    public function render()
    {
        return view('livewire.create-post');
    }
}
```

### Penjelasan:

| Konsep                                 | Deskripsi                        |
| -------------------------------------- | -------------------------------- |
| `WithFileUploads`                      | Trait untuk handle file upload   |
| `#[Layout]`                            | Menentukan layout untuk komponen |
| `#[Title]`                             | Set page title                   |
| `$this->redirect(..., navigate: true)` | SPA-style redirect               |

---

## 📝 Langkah 4.3: Edit View Komponen

Edit `resources/views/livewire/create-post.blade.php`:

```html
<div class="max-w-3xl mx-auto">
  <div class="bg-white rounded-lg shadow-lg overflow-hidden">
    <!-- Header -->
    <div class="bg-gradient-to-r from-blue-500 to-purple-600 px-6 py-4">
      <h1 class="text-2xl font-bold text-white">✍️ Tulis Artikel Baru</h1>
    </div>

    <!-- Form -->
    <form wire:submit="save" class="p-6 space-y-6">
      <!-- Title -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-2">
          Judul Artikel
        </label>
        <input
          type="text"
          wire:model="title"
          placeholder="Judul yang menarik..."
          class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent text-lg"
        />
        @error('title')
        <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
      </div>

      <!-- Image Upload -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-2">
          Gambar Cover (Opsional)
        </label>

        @if ($image)
        <!-- Preview -->
        <div class="relative mb-3">
          <img
            src="{{ $image->temporaryUrl() }}"
            class="w-full h-48 object-cover rounded-lg"
          />
          <button
            type="button"
            wire:click="removeImage"
            class="absolute top-2 right-2 bg-red-500 text-white rounded-full w-8 h-8 flex items-center justify-center hover:bg-red-600"
          >
            ✕
          </button>
        </div>
        @endif

        <div class="relative">
          <input
            type="file"
            wire:model="image"
            accept="image/*"
            class="w-full px-4 py-3 border-2 border-dashed border-gray-300 rounded-lg cursor-pointer hover:border-blue-500 transition-colors"
          />

          <!-- Upload Progress -->
          <div
            wire:loading
            wire:target="image"
            class="absolute inset-0 bg-white/80 flex items-center justify-center rounded-lg"
          >
            <div class="text-center">
              <div
                class="animate-spin inline-block w-8 h-8 border-4 border-blue-500 border-t-transparent rounded-full"
              ></div>
              <p class="mt-2 text-sm text-gray-500">Mengupload...</p>
            </div>
          </div>
        </div>

        @error('image')
        <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror

        <p class="mt-1 text-xs text-gray-500">
          Format: JPG, PNG, GIF. Maksimal 2MB.
        </p>
      </div>

      <!-- Content -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-2">
          Konten Artikel
        </label>
        <textarea
          wire:model="content"
          rows="15"
          placeholder="Tulis konten artikel Anda di sini..."
          class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
        ></textarea>
        @error('content')
        <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
        <p class="mt-1 text-xs text-gray-500">Minimal 50 karakter.</p>
      </div>

      <!-- Actions -->
      <div class="flex justify-end gap-3 pt-4 border-t">
        <a
          href="/posts"
          wire:navigate
          class="px-6 py-2 text-gray-600 hover:text-gray-800"
        >
          Batal
        </a>
        <button
          type="submit"
          class="px-6 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors disabled:opacity-50"
          wire:loading.attr="disabled"
          wire:target="save, image"
        >
          <span wire:loading.remove wire:target="save">🚀 Publish</span>
          <span wire:loading wire:target="save">Menyimpan...</span>
        </button>
      </div>
    </form>
  </div>
</div>
```

---

## 📝 Langkah 4.4: Tambahkan Route

Edit `routes/web.php`:

```php
use App\Livewire\CreatePost;

Route::get('/posts/create', CreatePost::class)
    ->middleware('auth')
    ->name('posts.create');
```

---

## 📝 Langkah 4.5: Test Upload

1. Login ke aplikasi
2. Klik "+ Tulis" di header
3. Isi form dan upload gambar
4. Perhatikan preview dan progress indicator

---

## 📝 Langkah 4.6: Konfigurasi Upload (Opsional)

Edit `config/livewire.php`:

```php
'temporary_file_upload' => [
    'disk' => 'local',
    'rules' => ['required', 'file', 'max:5120'], // Max 5MB default
    'directory' => 'livewire-tmp',
    'middleware' => null,
    'preview_mimes' => ['png', 'gif', 'bmp', 'svg', 'wav', 'mp4', 'mov', 'avi', 'wmv', 'mp3', 'jpg', 'jpeg', 'pdf'],
],
```

---

## ✅ Checkpoint

- [x] Form create post dengan validasi
- [x] File upload dengan preview
- [x] Progress indicator
- [x] Redirect setelah publish

---

## ⬅️ [Step 3: Post List](03-post-list-pagination.md) | ➡️ [Step 5: Show Post & Comments](05-show-post-comments.md)
