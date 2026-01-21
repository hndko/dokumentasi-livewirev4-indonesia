# Properties

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Properties menyimpan dan mengelola state di dalam komponen Livewire Anda. Mereka didefinisikan sebagai properti public pada class komponen dan dapat diakses serta dimodifikasi baik di sisi server maupun client.

---

## Daftar Isi

- [Menginisialisasi Properties](#menginisialisasi-properties)
- [Bulk Assignment](#bulk-assignment)
- [Data Binding](#data-binding)
- [Mereset Properties](#mereset-properties)
- [Pulling Properties](#pulling-properties)
- [Tipe Property yang Didukung](#tipe-property-yang-didukung)
  - [Tipe Primitif](#tipe-primitif)
  - [Tipe PHP Umum](#tipe-php-umum)
  - [Mendukung Tipe Kustom](#mendukung-tipe-kustom)
- [Mengakses Properties dari JavaScript](#mengakses-properties-dari-javascript)
- [Masalah Keamanan](#masalah-keamanan)

---

## Menginisialisasi Properties

Anda dapat menyetel nilai awal untuk properti dalam method `mount()` komponen Anda.

Perhatikan contoh berikut:

```php
<?php
// resources/views/components/⚡todos.blade.php

use Livewire\Component;

new class extends Component {
    public $todos = [];
    public $todo = '';

    public function mount()
    {
        $this->todos = ['Beli bahan makanan', 'Jalan-jalan anjing', 'Tulis kode'];
    }

    // ...
};
```

Dalam contoh ini, kita telah mendefinisikan array `todos` kosong dan menginisialisasinya dengan daftar todos default di method `mount()`. Sekarang, saat komponen merender pertama kali, todos awal ini ditampilkan kepada pengguna.

---

## Bulk Assignment

Terkadang menginisialisasi banyak properti di method `mount()` bisa terasa verbose. Untuk membantu ini, Livewire menyediakan cara praktis untuk menetapkan beberapa properti sekaligus via method `fill()`. Dengan passing array asosiatif dari nama properti dan nilainya, Anda dapat menyetel beberapa properti secara simultan dan mengurangi baris-baris repetitif di `mount()`.

Misalnya:

```php
<?php
// resources/views/components/post/⚡edit.blade.php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public $post;
    public $title;
    public $description;

    public function mount(Post $post)
    {
        $this->post = $post;

        $this->fill(
            $post->only('title', 'description'),
        );
    }

    // ...
};
```

Karena `$post->only(...)` mengembalikan array asosiatif dari atribut model dan nilainya berdasarkan nama yang Anda passing, properti `$title` dan `$description` akan disetel awal ke title dan description dari model `$post` dari database tanpa harus menyetel masing-masing secara individual.

---

## Data Binding

Livewire mendukung data binding dua arah melalui atribut HTML `wire:model`. Ini memungkinkan Anda dengan mudah mensinkronkan data antara properti komponen dan input HTML, menjaga antarmuka pengguna dan state komponen tetap sinkron.

Mari gunakan direktif `wire:model` untuk bind properti `$todo` di komponen todos ke elemen input dasar:

```php
<?php
// resources/views/components/⚡todos.blade.php

use Livewire\Component;

new class extends Component {
    public $todos = [];
    public $todo = '';

    public function add()
    {
        $this->todos[] = $this->todo;
        $this->todo = '';
    }

    // ...
};
```

```html
<div>
  <input type="text" wire:model="todo" placeholder="Todo..." />

  <button wire:click="add">Tambah Todo</button>

  <ul>
    @foreach ($todos as $todo)
    <li wire:key="{{ $loop->index }}">{{ $todo }}</li>
    @endforeach
  </ul>
</div>
```

Dalam contoh di atas, nilai input teks akan disinkronkan dengan properti `$todo` di server saat tombol "Tambah Todo" diklik.

Ini baru menyentuh permukaan `wire:model`. Untuk informasi lebih dalam tentang data binding, lihat [dokumentasi tentang forms](./VIII.%20forms.md).

---

## Mereset Properties

Terkadang, Anda mungkin perlu mereset properti Anda kembali ke state awalnya setelah aksi dilakukan oleh pengguna. Dalam kasus ini, Livewire menyediakan method `reset()` yang menerima satu atau lebih nama properti dan mereset nilainya ke state awal.

Dalam contoh di bawah, kita dapat menghindari duplikasi kode dengan menggunakan `$this->reset()` untuk mereset field todo setelah tombol "Tambah Todo" diklik:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $todos = [];
    public $todo = '';

    public function addTodo()
    {
        $this->todos[] = $this->todo;

        $this->reset('todo');
    }

    // ...
};
```

Dalam contoh di atas, setelah pengguna mengklik "Tambah Todo", field input yang menampung todo yang baru ditambahkan akan dibersihkan, memungkinkan pengguna menulis todo baru.

> **Catatan:** `reset()` akan mereset properti ke state-nya sebelum method `mount()` dipanggil. Jika Anda menginisialisasi properti di `mount()` ke nilai berbeda, Anda perlu mereset properti secara manual.

---

## Pulling Properties

Alternatifnya, Anda dapat menggunakan method `pull()` untuk mereset dan mengambil nilai dalam satu operasi.

Berikut contoh yang sama dari atas, tetapi disederhanakan dengan `pull()`:

```php
<?php

use Livewire\Component;

new class extends Component {
    public $todos = [];
    public $todo = '';

    public function addTodo()
    {
        $this->todos[] = $this->pull('todo');
    }

    // ...
};
```

Contoh di atas menarik nilai tunggal, tetapi `pull()` juga dapat digunakan untuk mereset dan mengambil (sebagai key-value pair) semua atau beberapa properti:

```php
// Sama dengan $this->all() dan $this->reset();
$this->pull();

// Sama dengan $this->only(...) dan $this->reset(...);
$this->pull(['title', 'content']);
```

---

## Tipe Property yang Didukung

Livewire mendukung set terbatas tipe properti karena pendekatan uniknya dalam mengelola data komponen antara request server.

Setiap properti di komponen Livewire di-serialize atau "dehydrate" ke JSON antara request, kemudian "hydrate" dari JSON kembali ke PHP untuk request berikutnya.

Proses konversi dua arah ini memiliki batasan tertentu, membatasi tipe properti yang dapat digunakan Livewire.

### Tipe Primitif

Livewire mendukung tipe primitif seperti string, integer, dll. Tipe-tipe ini dapat dengan mudah dikonversi ke dan dari JSON, menjadikannya ideal untuk digunakan sebagai properti di komponen Livewire.

Livewire mendukung tipe properti primitif berikut: `Array`, `String`, `Integer`, `Float`, `Boolean`, dan `Null`.

```php
new class extends Component {
    public array $todos = [];
    public string $todo = '';
    public int $maxTodos = 10;
    public bool $showTodos = false;
    public ?string $todoFilter = null;
};
```

### Tipe PHP Umum

Selain tipe primitif, Livewire mendukung tipe object PHP umum yang digunakan di aplikasi Laravel. Namun, penting untuk dicatat bahwa tipe-tipe ini akan di-dehydrate ke JSON dan di-hydrate kembali ke PHP pada setiap request.

Tipe PHP yang didukung:

- `BackedEnum`
- `Illuminate\Support\Collection`
- `Illuminate\Database\Eloquent\Collection`
- `Illuminate\Database\Eloquent\Model`
- `DateTime`
- `Carbon\Carbon`
- `Illuminate\Support\Stringable`

> **Peringatan:** Saat menyimpan Eloquent Collections dan Models di properti Livewire:
>
> - **Query constraints tidak dipertahankan:** Constraint query tambahan seperti `select(...)` tidak akan diterapkan ulang pada request berikutnya.
> - **Dampak performa:** Menyimpan koleksi Eloquent besar sebagai properti dapat menyebabkan masalah performa. Untuk query mahal, pertimbangkan menggunakan computed properties.

Berikut contoh cepat menyetel properti sebagai berbagai tipe ini:

```php
public function mount()
{
    $this->todos = collect([]);           // Collection
    $this->todos = Todos::all();          // Eloquent Collection
    $this->todo = Todos::first();         // Model
    $this->date = new DateTime('now');    // DateTime
    $this->date = new Carbon('now');      // Carbon
    $this->todo = str('');                // Stringable
}
```

### Mendukung Tipe Kustom

Livewire memungkinkan aplikasi Anda mendukung tipe kustom melalui dua mekanisme powerful:

- **Wireables**
- **Synthesizers**

**Wireables** sederhana dan mudah digunakan untuk sebagian besar aplikasi.

Wireables adalah class manapun di aplikasi Anda yang mengimplementasikan interface `Wireable`.

Misalnya, bayangkan Anda memiliki object Customer di aplikasi yang berisi data utama tentang customer:

```php
class Customer
{
    protected $name;
    protected $age;

    public function __construct($name, $age)
    {
        $this->name = $name;
        $this->age = $age;
    }
}
```

Mencoba menyetel instance class ini ke properti komponen Livewire akan menghasilkan error. Namun, Anda dapat menyelesaikan ini dengan mengimplementasikan interface `Wireable` dan menambahkan method `toLivewire()` dan `fromLivewire()`:

```php
use Livewire\Wireable;

class Customer implements Wireable
{
    protected $name;
    protected $age;

    public function __construct($name, $age)
    {
        $this->name = $name;
        $this->age = $age;
    }

    public function toLivewire()
    {
        return [
            'name' => $this->name,
            'age' => $this->age,
        ];
    }

    public static function fromLivewire($value)
    {
        $name = $value['name'];
        $age = $value['age'];

        return new static($name, $age);
    }
}
```

Sekarang Anda dapat dengan bebas menyetel object `Customer` pada komponen Livewire dan Livewire akan tahu cara mengonversi object-object ini ke JSON dan kembali ke PHP.

---

## Mengakses Properties dari JavaScript

Karena properti Livewire juga tersedia di browser via JavaScript, Anda dapat mengakses dan memanipulasi representasi JavaScript mereka dari [AlpineJS](https://alpinejs.dev/).

Alpine adalah library JavaScript ringan yang disertakan dengan Livewire. Alpine menyediakan cara untuk membangun interaksi ringan ke komponen Livewire Anda tanpa membuat full server roundtrip.

### Mengakses Properties

Livewire mengekspos object magic `$wire` ke Alpine. Anda dapat mengakses object `$wire` dari ekspresi Alpine manapun di dalam komponen Livewire Anda.

Object `$wire` dapat diperlakukan seperti versi JavaScript dari komponen Livewire Anda. Ia memiliki semua properti dan method yang sama dengan versi PHP komponen.

Misalnya, kita dapat menggunakan `$wire` untuk menampilkan hitungan karakter live dari field input todo:

```html
<div>
  <input type="text" wire:model="todo" />

  Panjang karakter todo:
  <h2 x-text="$wire.todo.length"></h2>
</div>
```

Saat pengguna mengetik di field, panjang karakter todo saat ini yang ditulis akan ditampilkan dan diperbarui secara live di halaman, semuanya tanpa mengirim network request ke server.

### Memanipulasi Properties

Demikian pula, Anda dapat memanipulasi properti komponen Livewire di JavaScript menggunakan `$wire`.

Misalnya, mari tambahkan tombol "Bersihkan" ke komponen todos untuk memungkinkan pengguna mereset field input hanya menggunakan JavaScript:

```html
<div>
  <input type="text" wire:model="todo" />

  <button x-on:click="$wire.todo = ''">Bersihkan</button>
</div>
```

Setelah pengguna mengklik "Bersihkan", input akan direset ke string kosong, tanpa mengirim network request ke server.

Jika Anda lebih suka, Anda juga dapat menggunakan method `.set()` yang lebih eksplisit untuk menyetel properti di sisi client:

```html
<button x-on:click="$wire.set('todo', '')">Bersihkan</button>
```

Untuk mengupdate properti tanpa mengirim network request ke server, Anda dapat passing parameter bool ketiga:

```html
<button x-on:click="$wire.set('todo', '', false)">Bersihkan</button>
```

---

## Masalah Keamanan

### Jangan Percaya Nilai Property

Selalu perlakukan properti public sebagai input pengguna — seolah-olah mereka adalah request input dari endpoint tradisional. Oleh karena itu, penting untuk memvalidasi dan mengotorisasi properti sebelum mempersist ke database — seperti yang akan Anda lakukan saat bekerja dengan request input di controller.

Untuk mendemonstrasikan bagaimana mengabaikan otorisasi dan validasi properti dapat memperkenalkan lubang keamanan, perhatikan komponen `post.edit` yang rentan berikut:

```php
<?php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public $id;
    public $title;
    public $content;

    public function mount(Post $post)
    {
        $this->id = $post->id;
        $this->title = $post->title;
        $this->content = $post->content;
    }

    public function update()
    {
        $post = Post::findOrFail($this->id);

        $post->update([
            'title' => $this->title,
            'content' => $this->content,
        ]);
    }
};
```

Pengguna jahat dapat dengan mudah mengubah properti `$id` dari sisi client.

**Mengotorisasi input:**

```php
public function update()
{
    $post = Post::findOrFail($this->id);

    $this->authorize('update', $post);

    $post->update(...);
}
```

**Mengunci properti:**

Livewire memungkinkan Anda "mengunci" properti untuk mencegah properti dimodifikasi di sisi client menggunakan atribut `#[Locked]`:

```php
use Livewire\Attributes\Locked;
use Livewire\Component;

new class extends Component {
    #[Locked]
    public $id;

    // ...
};
```

Dengan menggunakan `#[Locked]`, Anda dapat mengasumsikan properti ini tidak dimanipulasi di luar class komponen Anda.

### Model Eloquent dan Penguncian

Saat model Eloquent ditetapkan ke properti komponen Livewire, Livewire akan otomatis mengunci properti dan memastikan ID tidak diubah:

```php
<?php

use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public Post $post;  // Otomatis dikunci
    public $title;
    public $content;

    public function mount(Post $post)
    {
        $this->post = $post;
        $this->title = $post->title;
        $this->content = $post->content;
    }

    public function update()
    {
        $this->post->update([
            'title' => $this->title,
            'content' => $this->content,
        ]);
    }
};
```

### Properties Mengekspos Informasi Sistem ke Browser

Properti Livewire di-serialize atau "dehydrate" sebelum dikirim ke browser. Ini berarti nilainya dikonversi ke format yang dapat dikirim melalui jaringan dan dipahami oleh JavaScript. Format ini dapat mengekspos informasi tentang aplikasi Anda ke browser, termasuk nama dan nama class properti Anda.

Jika Anda tidak ingin mengekspos nama class model, Anda dapat menggunakan fungsionalitas "morphMap" Laravel dari service provider:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Database\Eloquent\Relations\Relation;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Relation::morphMap([
            'post' => 'App\Models\Post',
        ]);
    }
}
```

### Constraint Eloquent Tidak Dipertahankan Antar Request

Constraint query tambahan seperti `select(...)` tidak akan diterapkan ulang pada request berikutnya.

Untuk memastikan integritas query Eloquent, kami merekomendasikan Anda menggunakan **computed properties** sebagai gantinya.

Computed properties adalah method di komponen Anda yang ditandai dengan atribut `#[Computed]`. Mereka dapat diakses sebagai properti dinamis yang tidak disimpan sebagai bagian dari state komponen tetapi dievaluasi secara on-the-fly.

```php
<?php

use Illuminate\Support\Facades\Auth;
use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function todos()
    {
        return Auth::user()
            ->todos()
            ->select(['title', 'content'])
            ->get();
    }
};
```

Akses dari Blade view:

```html
<ul>
  @foreach ($this->todos as $todo)
  <li wire:key="{{ $loop->index }}">{{ $todo }}</li>
  @endforeach
</ul>
```

**Mengapa Menggunakan Computed Properties?**

Computed properties memiliki keuntungan performa, karena mereka otomatis di-memoize setelah penggunaan pertama selama satu request. Ini berarti Anda dapat dengan bebas mengakses `$this->todos` dalam komponen Anda dan yakin bahwa method sebenarnya hanya akan dipanggil sekali.
