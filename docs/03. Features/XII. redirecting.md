# Redirecting

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memberikan cara mudah untuk redirect pengguna ke URL lain setelah aksi dilakukan, seperti submit form atau proses lainnya.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Redirect ke Route](#redirect-ke-route)
- [Redirect ke Intended](#redirect-ke-intended)
- [Redirect ke Komponen Full-page](#redirect-ke-komponen-full-page)
- [Redirect ke Controller Actions](#redirect-ke-controller-actions)
- [Flash Messages](#flash-messages)

---

## Penggunaan Dasar

Gunakan method `redirect()` untuk redirect pengguna:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title = '';
    public $content = '';

    public function save()
    {
        // Simpan data...

        return $this->redirect('/posts');
    }
};
```

Dengan navigasi SPA (menggunakan wire:navigate):

```php
return $this->redirect('/posts', navigate: true);
```

---

## Redirect ke Route

Gunakan `redirectRoute()` untuk redirect ke named route:

```php
public function save()
{
    // Simpan data...

    return $this->redirectRoute('posts.index');
}
```

Dengan parameter route:

```php
return $this->redirectRoute('posts.show', ['post' => $post->id]);
```

Dengan navigasi SPA:

```php
return $this->redirectRoute('posts.index', navigate: true);
```

---

## Redirect ke Intended

Untuk redirect ke URL yang pengguna coba akses sebelum login:

```php
public function login()
{
    // Autentikasi...

    return $this->redirectIntended('/dashboard');
}
```

---

## Redirect ke Komponen Full-page

Untuk redirect ke komponen Livewire yang dirender sebagai halaman penuh:

```php
return $this->redirect(ShowPost::class);
```

---

## Redirect ke Controller Actions

```php
return $this->redirectAction(PostController::class, 'index');
```

Dengan parameter:

```php
return $this->redirectAction([PostController::class, 'show'], ['post' => $post->id]);
```

---

## Flash Messages

Untuk menampilkan pesan setelah redirect:

```php
public function save()
{
    // Simpan data...

    session()->flash('message', 'Post berhasil disimpan!');

    return $this->redirect('/posts');
}
```

Menampilkan flash message di view:

```html
@if (session()->has('message'))
<div class="alert alert-success">{{ session('message') }}</div>
@endif
```

### Contoh Lengkap

```php
<?php

use Livewire\Attributes\Validate;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    #[Validate('required|min:5')]
    public $title = '';

    #[Validate('required')]
    public $content = '';

    public function save()
    {
        $this->validate();

        $post = Post::create([
            'title' => $this->title,
            'content' => $this->content,
        ]);

        session()->flash('message', 'Post berhasil dibuat!');

        return $this->redirectRoute('posts.show', ['post' => $post->id], navigate: true);
    }

    public function cancel()
    {
        return $this->redirect('/posts', navigate: true);
    }
};
?>

<form wire:submit="save">
    <input wire:model="title" type="text" placeholder="Title">
    @error('title') <span class="error">{{ $message }}</span> @enderror

    <textarea wire:model="content" placeholder="Content"></textarea>
    @error('content') <span class="error">{{ $message }}</span> @enderror

    <button type="submit">Save</button>
    <button type="button" wire:click="cancel">Cancel</button>
</form>
```
