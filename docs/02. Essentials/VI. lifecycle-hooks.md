# Lifecycle Hooks

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire menyediakan berbagai lifecycle hooks yang memungkinkan Anda mengeksekusi kode pada titik-titik tertentu selama siklus hidup komponen. Hooks ini memungkinkan Anda melakukan aksi sebelum atau sesudah event tertentu, seperti menginisialisasi komponen, mengupdate properti, atau merender template.

---

## Daftar Isi

- [Daftar Lifecycle Hooks](#daftar-lifecycle-hooks)
- [Mount](#mount)
- [Boot](#boot)
- [Update](#update)
  - [Array](#array)
- [Hydrate & Dehydrate](#hydrate--dehydrate)
- [Render](#render)
- [Exception](#exception)
- [Menggunakan Hooks di dalam Trait](#menggunakan-hooks-di-dalam-trait)
- [Menggunakan Hooks di dalam Form Object](#menggunakan-hooks-di-dalam-form-object)

---

## Daftar Lifecycle Hooks

Berikut daftar semua lifecycle hooks komponen yang tersedia:

| Hook                              | Deskripsi                                           |
| --------------------------------- | --------------------------------------------------- |
| `mount()`                         | Dipanggil saat komponen pertama kali diinisialisasi |
| `hydrate()`                       | Dipanggil di awal setiap request "berikutnya"       |
| `boot()`                          | Dipanggil di awal setiap request (termasuk mount)   |
| `updating()`                      | Dipanggil sebelum properti diupdate                 |
| `updated()`                       | Dipanggil setelah properti diupdate                 |
| `rendering()`                     | Dipanggil sebelum view dirender                     |
| `render()`                        | Merender view Blade                                 |
| `rendered()`                      | Dipanggil setelah view dirender                     |
| `dehydrate()`                     | Dipanggil di akhir setiap request                   |
| `exception($e, $stopPropagation)` | Dipanggil saat exception terjadi                    |

---

## Mount

Dalam class PHP standar, constructor (`__construct()`) menerima parameter luar dan menginisialisasi state objek. Namun, di Livewire, Anda menggunakan method `mount()` untuk menerima parameter dan menginisialisasi state komponen.

Komponen Livewire tidak menggunakan `__construct()` karena komponen Livewire di-construct ulang pada network request berikutnya, dan kita hanya ingin menginisialisasi komponen sekali saat pertama kali dibuat.

**Contoh menginisialisasi properti:**

```php
<?php

use Illuminate\Support\Facades\Auth;
use Livewire\Component;

new class extends Component {
    public $name;
    public $email;

    public function mount()
    {
        $this->name = Auth::user()->name;
        $this->email = Auth::user()->email;
    }

    // ...
};
```

**Menerima data dari komponen parent:**

```php
<?php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public $title;
    public $content;

    public function mount(Post $post)
    {
        $this->title = $post->title;
        $this->content = $post->content;
    }

    // ...
};
```

Livewire memungkinkan Anda me-resolve dependency dari service container Laravel dengan type-hint parameter method pada lifecycle hooks.

---

## Boot

Method `mount()` hanya berjalan sekali per lifecycle komponen. Jika Anda ingin menjalankan logika di awal setiap request ke server untuk komponen tertentu, gunakan method `boot()`.

Method `boot()` berguna untuk hal-hal seperti menginisialisasi properti protected:

```php
<?php

use Livewire\Attributes\Locked;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    #[Locked]
    public $postId = 1;

    protected Post $post;

    public function boot()
    {
        $this->post = Post::find($this->postId);
    }

    // ...
};
```

> **Tips:** Teknik di atas powerful, namun sering lebih baik menggunakan [computed properties](https://livewire.laravel.com/docs/4.x/computed-properties).

> **Penting:** Kami menggunakan atribut `#[Locked]` pada properti `$postId`. Dalam skenario seperti di atas, penting untuk mengotorisasi nilai properti sebelum menggunakannya atau menambahkan `#[Locked]` untuk memastikan tidak pernah diubah.

---

## Update

Pengguna sisi client dapat mengupdate properti public dengan berbagai cara, paling umum dengan memodifikasi input dengan `wire:model`.

Livewire menyediakan hooks untuk mengintersep updating properti public:

**Mencegah modifikasi:**

```php
<?php

use Exception;
use Livewire\Component;

new class extends Component {
    public $postId = 1;

    public function updating($property, $value)
    {
        // $property: Nama properti yang sedang diupdate
        // $value: Nilai yang akan disetel ke properti

        if ($property === 'postId') {
            throw new Exception;
        }
    }

    // ...
};
```

**Memformat nilai setelah update:**

```php
<?php

use Livewire\Component;

new class extends Component {
    public $username = '';
    public $email = '';

    public function updated($property)
    {
        if ($property === 'username') {
            $this->username = strtolower($this->username);
        }
    }

    // ...
};
```

**Menargetkan properti spesifik:**

```php
public function updatedUsername()
{
    $this->username = strtolower($this->username);
}
```

### Array

Properti array memiliki argumen `$key` tambahan:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $preferences = [];

    public function updatedPreferences($value, $key)
    {
        // $value = 'dark'
        // $key = 'theme'
    }

    // ...
};
```

---

## Hydrate & Dehydrate

Istilah "dehydrate" dan "hydrate" merujuk pada proses komponen Livewire yang di-serialize ke JSON untuk sisi client dan kemudian di-unserialize kembali ke objek PHP pada request berikutnya.

```php
<?php

use Livewire\Component;

new class extends Component {
    public $post;

    public function mount($title, $content)
    {
        // Berjalan di awal request awal pertama...
        $this->post = new PostDto([
            'title' => $title,
            'content' => $content,
        ]);
    }

    public function hydrate()
    {
        // Berjalan di awal setiap request "berikutnya"...
        // Ini tidak berjalan pada request awal ("mount" yang berjalan)...
        $this->post = new PostDto($this->post);
    }

    public function dehydrate()
    {
        // Berjalan di akhir setiap request...
        $this->post = $this->post->toArray();
    }

    // ...
};
```

> **Catatan:** Untuk kasus seperti ini, direkomendasikan menggunakan [Wireables atau Synthesizers](https://livewire.laravel.com/docs/4.x/properties#supporting-custom-types).

---

## Render

Jika Anda ingin hook ke proses rendering view Blade komponen:

```php
<?php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public function render()
    {
        return $this->view([
            'post' => Post::all(),
        ]);
    }

    public function rendering($view, $data)
    {
        // Berjalan SEBELUM view yang disediakan dirender...
        // $view: View yang akan dirender
        // $data: Data yang diberikan ke view
    }

    public function rendered($view, $html)
    {
        // Berjalan SETELAH view yang disediakan dirender...
        // $view: View yang dirender
        // $html: HTML final yang dirender
    }

    // ...
};
```

---

## Exception

Hook `exception()` memungkinkan Anda mengintersep dan menangkap error:

```php
<?php

use Livewire\Component;

new class extends Component {
    public function mount()
    {
        $this->post = Post::find($this->postId);
    }

    public function exception($e, $stopPropagation)
    {
        if ($e instanceof NotFoundException) {
            $this->notify('Post tidak ditemukan');
            $stopPropagation();
        }
    }

    // ...
};
```

---

## Menggunakan Hooks di dalam Trait

Untuk menghindari konflik beberapa trait saat mendeklarasikan method lifecycle hook, Livewire mendukung prefix method hook dengan nama trait dalam camelCase:

```php
<?php

use Livewire\Component;

new class extends Component {
    use HasPostForm;

    // ...
};
```

```php
trait HasPostForm
{
    public $title = '';
    public $content = '';

    public function mountHasPostForm()
    {
        // ...
    }

    public function hydrateHasPostForm()
    {
        // ...
    }

    public function bootHasPostForm()
    {
        // ...
    }

    public function updatingHasPostForm()
    {
        // ...
    }

    public function updatedHasPostForm()
    {
        // ...
    }

    public function renderingHasPostForm()
    {
        // ...
    }

    public function renderedHasPostForm()
    {
        // ...
    }

    public function dehydrateHasPostForm()
    {
        // ...
    }

    // ...
}
```

---

## Menggunakan Hooks di dalam Form Object

Form object di Livewire mendukung property update hooks:

```php
<?php

use Livewire\Component;

new class extends Component {
    public PostForm $form;

    // ...
};
```

```php
namespace App\Livewire\Forms;

use Livewire\Attributes\Validate;
use Livewire\Form;

class PostForm extends Form
{
    public $title = '';
    public $tags = [];

    public function updating($property, $value)
    {
        // ...
    }

    public function updated($property, $value)
    {
        // ...
    }

    public function updatingTitle($value)
    {
        // ...
    }

    public function updatedTitle($value)
    {
        // ...
    }

    public function updatingTags($value, $key)
    {
        // ...
    }

    public function updatedTags($value, $key)
    {
        // ...
    }

    // ...
}
```
