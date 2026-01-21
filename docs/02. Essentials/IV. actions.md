# Actions

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire actions adalah method pada komponen Anda yang dapat dipicu oleh interaksi frontend seperti mengklik tombol atau mengirim form. Mereka memberikan pengalaman developer untuk dapat memanggil method PHP langsung dari browser, memungkinkan Anda fokus pada logika aplikasi tanpa terjebak menulis kode boilerplate yang menghubungkan frontend dan backend aplikasi.

---

## Daftar Isi

- [Contoh Dasar](#contoh-dasar)
- [Passing Parameter](#passing-parameter)
- [Dependency Injection](#dependency-injection)
- [Event Listeners](#event-listeners)
  - [Mendengarkan Key Spesifik](#mendengarkan-key-spesifik)
  - [Modifier Event Handler](#modifier-event-handler)
  - [Menangani Event Pihak Ketiga](#menangani-event-pihak-ketiga)
  - [Mendengarkan Event Kustom yang Didispatch](#mendengarkan-event-kustom-yang-didispatch)
  - [Menonaktifkan Input Saat Form Sedang Dikirim](#menonaktifkan-input-saat-form-sedang-dikirim)
- [Merefresh Komponen](#merefresh-komponen)
- [Mengkonfirmasi Aksi](#mengkonfirmasi-aksi)
- [Memanggil Aksi dari Alpine](#memanggil-aksi-dari-alpine)
- [Magic Actions](#magic-actions)
- [Melewati Re-render](#melewati-re-render)
- [Eksekusi Paralel dengan Async](#eksekusi-paralel-dengan-async)

---

## Contoh Dasar

Mari kita jelajahi contoh dasar memanggil aksi `save`:

```php
<?php
// resources/views/components/post/⚡create.blade.php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public $title = '';
    public $content = '';

    public function save()
    {
        Post::create([
            'title' => $this->title,
            'content' => $this->content,
        ]);

        return redirect()->to('/posts');
    }
};
?>

<form wire:submit="save">
    <input type="text" wire:model="title">
    <textarea wire:model="content"></textarea>
    <button type="submit">Save</button>
</form>
```

Dalam contoh di atas, saat pengguna mengirim form dengan mengklik "Save", `wire:submit` mengintersep event submit dan memanggil aksi `save()` di server.

Pada intinya, aksi adalah cara untuk dengan mudah memetakan interaksi pengguna ke fungsionalitas sisi server tanpa repot mengirim dan menangani request AJAX secara manual.

---

## Passing Parameter

Livewire memungkinkan Anda untuk passing parameter dari template Blade ke aksi di komponen Anda.

```php
<?php

use App\Models\Post;
use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function posts()
    {
        return Auth::user()->posts;
    }

    public function delete($id)
    {
        $post = Post::findOrFail($id);
        $this->authorize('delete', $post);
        $post->delete();
    }
};
```

```html
<div>
  @foreach ($this->posts as $post)
  <div wire:key="{{ $post->id }}">
    <h1>{{ $post->title }}</h1>
    <span>{{ $post->content }}</span>
    <button wire:click="delete({{ $post->id }})">Delete</button>
  </div>
  @endforeach
</div>
```

> **Peringatan Keamanan:** Parameter aksi harus diperlakukan seperti input HTTP request. Anda harus selalu mengotorisasi kepemilikan entitas sebelum mengupdate di database.

Sebagai kemudahan tambahan, Anda dapat otomatis resolve model Eloquent dengan type-hint:

```php
public function delete(Post $post)
{
    $this->authorize('delete', $post);
    $post->delete();
}
```

---

## Dependency Injection

Anda dapat memanfaatkan sistem dependency injection Laravel dengan type-hint parameter di signature aksi:

```php
public function delete(PostRepository $posts, $postId)
{
    $posts->deletePost($postId);
}
```

---

## Event Listeners

Livewire mendukung berbagai event listener:

- `wire:click`
- `wire:submit`
- `wire:keydown`
- `wire:keyup`
- `wire:mouseenter`
- `wire:*` (event browser apa pun)

### Mendengarkan Key Spesifik

Untuk melakukan pencarian saat pengguna menekan Enter:

```html
<input wire:model="query" wire:keydown.enter="searchPosts" />
```

Kombinasi key:

```html
<input wire:keydown.shift.enter="..." />
```

**Modifier key yang tersedia:** `.shift`, `.enter`, `.space`, `.ctrl`, `.cmd`, `.meta`, `.alt`, `.up`, `.down`, `.left`, `.right`, `.escape`, `.tab`, `.caps-lock`, `.equal`, `.period`, `.slash`

### Modifier Event Handler

- `.prevent` - Memanggil `event.preventDefault()`
- `.stop` - Memanggil `event.stopPropagation()`
- `.window` - Mendengarkan di objek `window`
- `.outside` - Mendengarkan klik di luar elemen
- `.document` - Mendengarkan di objek `document`
- `.once` - Hanya fire sekali
- `.debounce` - Debounce event
- `.throttle` - Throttle event
- `.self` - Hanya fire jika target adalah elemen ini
- `.camel` - Konversi nama event ke camelCase
- `.dot` - Konversi nama event ke dot notation
- `.passive` - Gunakan passive event listener
- `.capture` - Gunakan capture mode

### Menangani Event Pihak Ketiga

```html
<trix-editor
  wire:trix-change="setPostContent($event.target.value)"
></trix-editor>
```

> **Catatan:** `$event` memberikan akses ke objek event JavaScript.

### Mendengarkan Event Kustom yang Didispatch

```html
<div wire:custom-event="...">
  <button x-on:click="$dispatch('custom-event')">...</button>
</div>
```

Untuk mendengarkan event dari luar komponen:

```html
<div wire:custom-event.window="...">
  <!-- ... -->
</div>
```

### Menonaktifkan Input Saat Form Sedang Dikirim

Livewire otomatis menonaktifkan tombol submit dan semua input form di dalam elemen `<form>` saat aksi `wire:submit` sedang diproses.

Untuk menampilkan indikator loading:

```html
<form wire:submit="save">
  <textarea wire:model="content"></textarea>
  <button type="submit">Save</button>
  <span wire:loading>Saving...</span>
</form>
```

Atau menggunakan Tailwind dan atribut `data-loading`:

```html
<button type="submit" class="data-loading:opacity-50">Save</button>
<span class="not-data-loading:hidden">Saving...</span>
```

---

## Merefresh Komponen

Gunakan aksi `$refresh` untuk memicu refresh komponen:

```html
<button type="button" wire:click="$refresh">...</button>
```

Atau dengan Alpine:

```html
<button type="button" x-on:click="$wire.$refresh()">...</button>
```

---

## Mengkonfirmasi Aksi

Gunakan `wire:confirm` untuk menampilkan dialog konfirmasi:

```html
<button
  type="button"
  wire:click="delete"
  wire:confirm="Apakah Anda yakin ingin menghapus post ini?"
>
  Hapus post
</button>
```

---

## Memanggil Aksi dari Alpine

Object magic `$wire` dapat digunakan untuk memanggil aksi:

```html
<button x-on:click="$wire.save()">Save Post</button>
```

Atau dengan `x-intersect`:

```html
<div x-intersect="$wire.incrementViewCount()">...</div>
```

---

## Magic Actions

Livewire menyediakan set aksi "magic":

### $parent

Memanggil aksi di komponen parent:

```html
<button wire:click="$parent.removePost({{ $post->id }})">Remove</button>
```

### $set

Mengupdate properti langsung dari template:

```html
<button wire:click="$set('query', '')">Reset Search</button>
```

### $refresh

Memicu re-render komponen:

```html
<button wire:click="$refresh">Refresh</button>
```

### $toggle

Toggle nilai properti boolean:

```html
<button wire:click="$toggle('sortAsc')">
  Sort {{ $sortAsc ? 'Descending' : 'Ascending' }}
</button>
```

### $dispatch

Mendispatch event Livewire:

```html
<button type="submit" wire:click="$dispatch('post-deleted')">
  Delete Post
</button>
```

### $event

Mengakses objek event JavaScript:

```html
<input type="text" wire:keydown.enter="search($event.target.value)" />
```

---

## Melewati Re-render

Gunakan atribut `#[Renderless]` untuk aksi tanpa side effect visual:

```php
use Livewire\Attributes\Renderless;

#[Renderless]
public function incrementViewCount()
{
    $this->post->incrementViewCount();
}
```

Atau dengan modifier `.renderless`:

```html
<button type="button" wire:click.renderless="incrementViewCount"></button>
```

---

## Eksekusi Paralel dengan Async

Secara default, Livewire men-serialize aksi dalam komponen yang sama. Gunakan `#[Async]` atau `.async` untuk eksekusi paralel.

### Menggunakan Modifier .async

```html
<button wire:click.async="logActivity">Track Event</button>
```

### Menggunakan Atribut Async

```php
use Livewire\Attributes\Async;

#[Async]
public function logActivity()
{
    Activity::log('post-viewed', $this->post);
}
```

### Kapan Menggunakan Async Actions

- **Analytics dan logging:** Melacak perilaku pengguna
- **Operasi background:** Memicu job, mengirim notifikasi
- **Hasil JavaScript-only:** Mengambil data via `await $wire.getData()`

```html
<a href="{{ $url }}" target="_blank" wire:click.async="trackClick">
  Visit External Site
</a>
```
