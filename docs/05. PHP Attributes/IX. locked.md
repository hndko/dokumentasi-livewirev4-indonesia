# #[Locked]

Atribut `#[Locked]` mencegah properti dimodifikasi dari frontend, memberikan keamanan tambahan untuk data sensitif.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Mengapa Tidak Menggunakan Protected?](#mengapa-tidak-menggunakan-protected)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Locked;
use Livewire\Component;

class ShowPost extends Component
{
    #[Locked]
    public $postId;

    public function mount($postId)
    {
        $this->postId = $postId;
    }

    public function delete()
    {
        Post::find($this->postId)->delete();

        return redirect('/posts');
    }
}
```

Jika pengguna mencoba mengubah `$postId` dari frontend, Livewire akan melempar exception.

---

## Kapan Menggunakan

- ID entitas yang tidak boleh diubah pengguna
- Data sensitif yang digunakan untuk operasi backend
- Properti yang menentukan akses atau kepemilikan

---

## Mengapa Tidak Menggunakan Protected?

Properti `protected` tidak dapat diakses di view Blade. `#[Locked]` memungkinkan properti tetap `public` untuk diakses di view, tetapi tidak dapat dimodifikasi dari frontend.

```php
// ❌ Tidak dapat diakses di view
protected $postId;

// ✅ Dapat diakses di view, tapi tidak dapat dimodifikasi dari frontend
#[Locked]
public $postId;
```

```html
<!-- Dapat menampilkan nilainya -->
<div>Post ID: {{ $postId }}</div>
```
