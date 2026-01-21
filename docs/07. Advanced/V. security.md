# Security

Panduan keamanan untuk aplikasi Livewire. Pelajari cara melindungi komponen dari manipulasi pengguna jahat.

---

## Daftar Isi

- [Mengotorisasi Parameter Aksi](#mengotorisasi-parameter-aksi)
- [Mengotorisasi Properti Publik](#mengotorisasi-properti-publik)
- [Middleware](#middleware)
- [Snapshot Checksums](#snapshot-checksums)

---

## Mengotorisasi Parameter Aksi

Parameter aksi dikirim dari browser dan bisa dimanipulasi. Selalu validasi dan otorisasi.

```php
public function delete($postId)
{
    // ❌ TIDAK AMAN
    Post::find($postId)->delete();

    // ✅ AMAN - Otorisasi kepemilikan
    $post = Post::find($postId);

    $this->authorize('delete', $post);

    $post->delete();
}
```

---

## Mengotorisasi Properti Publik

Properti publik dapat dimodifikasi dari frontend.

### Menggunakan Model Properties

```php
// ❌ TIDAK AMAN - pengguna bisa mengubah postId
public $postId;

public function delete()
{
    Post::find($this->postId)->delete();
}
```

### Mengunci Properti

```php
use Livewire\Attributes\Locked;

#[Locked]
public $postId;

public function delete()
{
    // postId tidak bisa diubah dari frontend
    Post::find($this->postId)->delete();
}
```

### Mengotorisasi Properti

```php
public function updating($property, $value)
{
    if ($property === 'postId') {
        $post = Post::find($value);
        $this->authorize('update', $post);
    }
}
```

---

## Middleware

### Configuring Persistent Middleware

Middleware yang tetap aktif antar request:

```php
// config/livewire.php
'middleware_group' => ['web', 'auth'],
```

### Applying Global Livewire Middleware

```php
// app/Providers/AppServiceProvider.php
Livewire::addPersistentMiddleware([
    EnsureEmailIsVerified::class,
]);
```

---

## Snapshot Checksums

Livewire menggunakan checksum untuk mencegah manipulasi snapshot:

- Snapshot di-sign dengan APP_KEY
- Perubahan akan terdeteksi
- Request akan ditolak jika checksum tidak valid

> **Penting:** Jangan pernah expose APP_KEY Anda.
