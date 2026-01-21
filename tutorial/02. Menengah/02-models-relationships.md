# Step 2: Models & Relationships

## 🎯 Tujuan

- Membuat model Post dan Comment
- Mengatur relasi Eloquent
- Menambahkan factory dan seeder

---

## 📝 Langkah 2.1: Buat Model Post

```bash
php artisan make:model Post -f
```

Flag `-f` juga membuat factory.

Edit `app/Models/Post.php`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'slug',
        'content',
        'image',
        'user_id',
    ];

    /**
     * Post dimiliki oleh satu User
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Post memiliki banyak Comment
     */
    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    /**
     * Generate slug dari title
     */
    public static function boot()
    {
        parent::boot();

        static::creating(function ($post) {
            $post->slug = \Str::slug($post->title);
        });
    }
}
```

---

## 📝 Langkah 2.2: Buat Model Comment

```bash
php artisan make:model Comment -f
```

Edit `app/Models/Comment.php`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Comment extends Model
{
    use HasFactory;

    protected $fillable = [
        'body',
        'post_id',
        'user_id',
    ];

    /**
     * Comment dimiliki oleh satu Post
     */
    public function post(): BelongsTo
    {
        return $this->belongsTo(Post::class);
    }

    /**
     * Comment dimiliki oleh satu User
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

---

## 📝 Langkah 2.3: Update User Model

Edit `app/Models/User.php`, tambahkan relasi:

```php
use Illuminate\Database\Eloquent\Relations\HasMany;

// ... existing code ...

/**
 * User memiliki banyak Post
 */
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}

/**
 * User memiliki banyak Comment
 */
public function comments(): HasMany
{
    return $this->hasMany(Comment::class);
}
```

---

## 📝 Langkah 2.4: Buat Factory Post

Edit `database/factories/PostFactory.php`:

```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class PostFactory extends Factory
{
    public function definition(): array
    {
        $title = fake()->sentence();

        return [
            'title' => $title,
            'slug' => Str::slug($title),
            'content' => fake()->paragraphs(5, true),
            'image' => null,
            'user_id' => User::factory(),
        ];
    }
}
```

---

## 📝 Langkah 2.5: Buat Factory Comment

Edit `database/factories/CommentFactory.php`:

```php
<?php

namespace Database\Factories;

use App\Models\Post;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class CommentFactory extends Factory
{
    public function definition(): array
    {
        return [
            'body' => fake()->paragraph(),
            'post_id' => Post::factory(),
            'user_id' => User::factory(),
        ];
    }
}
```

---

## 📝 Langkah 2.6: Buat Seeder

Edit `database/seeders/DatabaseSeeder.php`:

```php
<?php

namespace Database\Seeders;

use App\Models\Comment;
use App\Models\Post;
use App\Models\User;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Buat 5 user
        $users = User::factory(5)->create();

        // Buat 20 post dari user random
        $posts = Post::factory(20)->create([
            'user_id' => fn() => $users->random()->id,
        ]);

        // Buat 50 comment dari user random di post random
        Comment::factory(50)->create([
            'user_id' => fn() => $users->random()->id,
            'post_id' => fn() => $posts->random()->id,
        ]);

        // Buat user untuk login test
        User::factory()->create([
            'name' => 'Test User',
            'email' => 'test@example.com',
        ]);
    }
}
```

---

## 📝 Langkah 2.7: Jalankan Seeder

```bash
php artisan migrate:fresh --seed
```

---

## 📝 Langkah 2.8: Verifikasi di Tinker

```bash
php artisan tinker
```

```php
>>> App\Models\Post::count()
=> 20

>>> App\Models\Comment::count()
=> 50

>>> App\Models\Post::first()->comments->count()
=> 3

>>> exit
```

---

## ✅ Checkpoint

- [x] Model Post dengan relasi user & comments
- [x] Model Comment dengan relasi post & user
- [x] Factory untuk data dummy
- [x] Seeder dengan 20 posts dan 50 comments

---

## ⬅️ [Step 1: Setup](01-setup-database.md) | ➡️ [Step 3: Post List](03-post-list-pagination.md)
