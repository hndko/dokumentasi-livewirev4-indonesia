# Step 8: Testing & Finalisasi

## 🎯 Tujuan

- Menulis test untuk komponen
- Finalisasi project

---

## 📝 Langkah 8.1: Install Testing Dependencies

```bash
composer require --dev livewire/livewire
```

---

## 📝 Langkah 8.2: Test PostList

```php
// tests/Feature/PostListTest.php
<?php

namespace Tests\Feature;

use App\Livewire\PostList;
use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Livewire\Livewire;
use Tests\TestCase;

class PostListTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_search_posts()
    {
        Post::factory()->create(['title' => 'Laravel Tutorial']);
        Post::factory()->create(['title' => 'Vue.js Guide']);

        Livewire::test(PostList::class)
            ->set('search', 'Laravel')
            ->assertSee('Laravel Tutorial')
            ->assertDontSee('Vue.js Guide');
    }

    public function test_can_sort_posts()
    {
        Livewire::test(PostList::class)
            ->set('sortBy', 'popular')
            ->assertOk();
    }
}
```

---

## 📝 Langkah 8.3: Test CommentSection

```php
// tests/Feature/CommentTest.php
public function test_can_add_comment()
{
    $user = User::factory()->create();
    $post = Post::factory()->create();

    Livewire::actingAs($user)
        ->test(CommentSection::class, ['post' => $post])
        ->set('body', 'Great post!')
        ->call('addComment')
        ->assertSee('Great post!');
}
```

---

## 📝 Langkah 8.4: Jalankan Tests

```bash
php artisan test
```

---

## 🎉 Project Selesai!

### Fitur yang Dibuat:

- ✅ Post list dengan search & pagination
- ✅ Create post dengan file upload
- ✅ Show post dengan nested comments
- ✅ Real-time notifications
- ✅ SPA-like navigation

### Struktur Final:

```
app/Livewire/
├── PostList.php
├── CreatePost.php
├── ShowPost.php
├── CommentSection.php
└── Notifications.php
```

---

## ⬅️ [Step 7](07-spa-lazy-loading.md) | 🏠 [README](README.md)
