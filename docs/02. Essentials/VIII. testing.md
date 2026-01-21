# Testing

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Komponen Livewire mudah untuk ditest. Karena mereka hanyalah class Laravel biasa, mereka dapat ditest menggunakan alat testing Laravel yang sudah ada. Namun, Livewire menyediakan banyak utilitas tambahan untuk membuat testing komponen Anda menjadi mudah.

Dokumentasi ini akan memandu Anda melakukan testing komponen Livewire menggunakan Pest sebagai framework testing yang direkomendasikan, meskipun Anda juga dapat menggunakan PHPUnit jika Anda lebih suka.

---

## Daftar Isi

- [Menginstall Pest](#menginstall-pest)
- [Mengkonfigurasi Pest untuk Komponen View-based](#mengkonfigurasi-pest-untuk-komponen-view-based)
- [Membuat Test Pertama Anda](#membuat-test-pertama-anda)
- [Testing Halaman Berisi Komponen](#testing-halaman-berisi-komponen)
- [Memanggil Actions](#memanggil-actions)

---

## Menginstall Pest

[Pest](https://pestphp.com/) adalah framework testing PHP yang menyenangkan dengan fokus pada kesederhanaan. Ini adalah cara yang direkomendasikan untuk test komponen Livewire di Livewire 4.

Untuk menginstall Pest di aplikasi Laravel Anda, pertama hapus PHPUnit (jika sudah terinstall) dan require Pest:

```bash
composer remove phpunit/phpunit
composer require pestphp/pest --dev --with-all-dependencies
```

Selanjutnya, inisialisasi Pest di proyek Anda:

```bash
./vendor/bin/pest --init
```

Ini akan membuat file konfigurasi `tests/Pest.php` di proyek Anda.

Untuk instruksi instalasi lebih detail, lihat [dokumentasi instalasi Pest](https://pestphp.com/docs/installation).

---

## Mengkonfigurasi Pest untuk Komponen View-based

Jika Anda menulis test bersamaan dengan komponen view-based (single-file atau multi-file), Anda perlu mengkonfigurasi Pest untuk mengenali file test ini.

Pertama, update file `tests/Pest.php` Anda untuk menyertakan direktori `resources/views`:

```php
pest()->extend(Tests\TestCase::class)
    // ...
    ->in('Feature', '../resources/views');
```

Ini memberitahu Pest untuk menggunakan base class `TestCase` Anda untuk test yang ditemukan baik di direktori `tests/Feature` maupun di mana saja dalam `resources/views`.

Selanjutnya, update file `phpunit.xml` Anda untuk menyertakan test suite untuk test komponen:

```xml
<testsuite name="Components">
    <directory suffix=".test.php">resources/views</directory>
</testsuite>
```

Sekarang Pest akan mengenali dan menjalankan test yang terletak di samping komponen Anda saat Anda menjalankan `./vendor/bin/pest`.

---

## Membuat Test Pertama Anda

Anda dapat menghasilkan file test bersamaan dengan komponen dengan menambahkan flag `--test` ke perintah `make:livewire`:

```bash
php artisan make:livewire post.create --test
```

Untuk komponen multi-file, ini akan membuat file test di `resources/views/components/post/create.test.php`:

```php
<?php

use Livewire\Livewire;

it('renders successfully', function () {
    Livewire::test('post.create')
        ->assertStatus(200);
});
```

Untuk komponen berbasis class, ini membuat file test PHPUnit di `tests/Feature/Livewire/Post/CreateTest.php`. Anda dapat mengonversinya ke syntax Pest atau tetap menggunakan PHPUnit—keduanya bekerja dengan baik dengan Livewire.

---

## Testing Halaman Berisi Komponen

Test Livewire paling sederhana yang dapat Anda tulis adalah memastikan bahwa endpoint tertentu menyertakan dan berhasil merender komponen Livewire.

```php
it('component exists on the page', function () {
    $this->get('/posts/create')
        ->assertSeeLivewire('post.create');
});
```

Test seperti ini disebut "smoke tests" — mereka memastikan tidak ada masalah katastropik di aplikasi Anda. Meskipun sederhana, test ini memberikan nilai yang sangat besar karena memerlukan sedikit maintenance dan memberi Anda tingkat kepercayaan dasar bahwa halaman Anda berhasil dirender.

---

## Memanggil Actions

Gunakan method `call()` untuk memicu aksi komponen dalam test Anda:

```php
use App\Models\Post;

it('can create a post', function () {
    expect(Post::count())->toBe(0);

    Livewire::test('post.create')
        ->set('title', 'My new post')
        ->set('content', 'Post content here')
        ->call('save');

    expect(Post::count())->toBe(1);
});
```

> **Catatan:** Contoh di atas menggunakan syntax `expect()` Pest untuk assertions. Untuk daftar lengkap expectations yang tersedia, lihat [dokumentasi expectations Pest](https://pestphp.com/docs/expectations).

Anda dapat passing parameter ke actions:

```php
Livewire::test('post.show')
    ->call('deletePost', $postId);
```

---

## Method Testing yang Tersedia

Berikut beberapa method testing Livewire yang umum digunakan:

### Setting Properties

```php
Livewire::test('post.create')
    ->set('title', 'Hello World')
    ->set('content', 'Lorem ipsum...');
```

### Testing Validation

```php
Livewire::test('post.create')
    ->set('title', '')  // Title kosong
    ->call('save')
    ->assertHasErrors(['title' => 'required']);
```

### Testing Redirects

```php
Livewire::test('post.create')
    ->set('title', 'My Post')
    ->call('save')
    ->assertRedirect('/posts');
```

### Testing dengan Authentication

```php
it('can edit own post', function () {
    $user = User::factory()->create();
    $post = Post::factory()->for($user)->create();

    Livewire::actingAs($user)
        ->test('post.edit', ['post' => $post])
        ->set('title', 'Updated Title')
        ->call('update')
        ->assertHasNoErrors();

    expect($post->fresh()->title)->toBe('Updated Title');
});
```

### Testing Events

```php
Livewire::test('post.create')
    ->call('save')
    ->assertDispatched('post-saved');
```

### Testing View Data

```php
Livewire::test('post.show', ['post' => $post])
    ->assertSee($post->title)
    ->assertSee($post->content);
```

---

## Best Practices

1. **Buat test sederhana dan fokus**: Setiap test harus menguji satu hal.

2. **Gunakan factories**: Gunakan Laravel factories untuk membuat data test.

3. **Test edge cases**: Jangan hanya test happy path, test juga error cases.

4. **Jalankan test sering**: Jalankan test suite Anda secara teratur selama development.

5. **Gunakan database transactions**: Laravel's `RefreshDatabase` trait memastikan database clean antar test.

```php
// tests/Pest.php
uses(RefreshDatabase::class)->in('Feature');
```
