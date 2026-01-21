# Nesting Components

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire memungkinkan Anda menyarangkan komponen Livewire tambahan di dalam komponen parent. Fitur ini sangat powerful, karena memungkinkan Anda menggunakan kembali dan mengenkapsulasi perilaku dalam komponen Livewire yang dibagikan di seluruh aplikasi Anda.

Sebelum Anda mengekstrak bagian template Anda ke komponen Livewire bersarang, tanyakan pada diri sendiri: Apakah konten di komponen ini perlu "live"? Jika tidak, kami merekomendasikan Anda membuat [Blade component](https://laravel.com/docs/blade#components) sederhana sebagai gantinya. Hanya buat komponen Livewire jika komponen mendapat manfaat dari sifat dinamis Livewire atau jika ada manfaat performa langsung.

Jika Anda ingin mengisolasi re-rendering ke region tertentu dari komponen Anda tanpa overhead membuat komponen child terpisah, pertimbangkan menggunakan [islands](https://livewire.laravel.com/docs/4.x/islands).

---

## Daftar Isi

- [Menyarangkan Komponen](#menyarangkan-komponen)
- [Passing Props ke Children](#passing-props-ke-children)
- [Merender Children dalam Loop](#merender-children-dalam-loop)
- [Reactive Props](#reactive-props)
- [Slots](#slots)
- [Mendengarkan Event dari Children](#mendengarkan-event-dari-children)

---

## Menyarangkan Komponen

Untuk menyarangkan komponen Livewire di dalam komponen parent, cukup sertakan di view Blade komponen parent:

```php
<?php
// resources/views/components/⚡dashboard.blade.php

use Livewire\Component;

new class extends Component {
    //
};
?>

<div>
    <h1>Dashboard</h1>

    <livewire:todos />
</div>
```

Pada render awal halaman ini, komponen `dashboard` akan menemukan `<livewire:todos />` dan merendernya. Pada subsequent network request ke `dashboard`, komponen `todos` bersarang akan melewatkan rendering karena sekarang menjadi komponen independennya sendiri di halaman.

---

## Passing Props ke Children

Passing data dari komponen parent ke komponen child sangat mudah. Sangat mirip dengan passing props ke Blade component biasa.

```php
<?php
// resources/views/components/⚡todos.blade.php

use Illuminate\Support\Facades\Auth;
use Livewire\Attributes\Computed;
use Livewire\Component;

new class extends Component {
    #[Computed]
    public function todos()
    {
        return Auth::user()->todos;
    }
};
?>

<div>
    <livewire:todo-count :todos="$this->todos" />

    <!-- ... -->
</div>
```

Terima data melalui method `mount()` komponen child:

```php
<?php
// resources/views/components/⚡todo-count.blade.php

use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Todo;

new class extends Component {
    public $todos;

    public function mount($todos)
    {
        $this->todos = $todos;
    }

    #[Computed]
    public function count()
    {
        return $this->todos->count();
    }
};
?>

<div>
    Count: {{ $this->count }}
</div>
```

Jika method `mount()` terasa seperti kode boilerplate berlebihan, bisa dihilangkan selama nama properti dan parameter cocok:

```php
public $todos;
```

---

## Merender Children dalam Loop

Saat merender komponen child dalam loop, Anda harus menyertakan nilai key unik untuk setiap iterasi.

```html
<div>
  <h1>Todos</h1>

  @foreach ($todos as $todo)
  <livewire:todo-item :$todo :wire:key="$todo->id" />
  @endforeach
</div>
```

Seperti yang Anda lihat, setiap komponen child akan memiliki key unik yang disetel ke ID setiap `$todo`. Ini memastikan key akan unik dan dilacak jika todos diurutkan ulang.

> **Penting:** Berbeda dengan framework frontend seperti Vue atau Alpine, Livewire sangat bergantung pada key dan akan berperilaku salah tanpanya.

---

## Reactive Props

Developer baru di Livewire mengharapkan bahwa props adalah "reactive" secara default. Dengan kata lain, mereka mengharapkan bahwa ketika parent mengubah nilai prop yang di-pass ke komponen child, komponen child akan otomatis diupdate. Namun, secara default, props Livewire tidak reactive.

Di Livewire, [setiap komponen adalah independen](https://livewire.laravel.com/docs/4.x/understanding-nesting#every-component-is-an-island). Ini berarti saat update dipicu pada parent dan network request dikirim, hanya state komponen parent yang dikirim ke server untuk di-render ulang — bukan state komponen child.

Tapi, jika Anda menginginkan prop menjadi reactive, Anda dapat dengan mudah mengaktifkan perilaku ini menggunakan atribut `#[Reactive]`:

```php
<?php
// resources/views/components/⚡todo-count.blade.php

use Livewire\Attributes\Reactive;
use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Todo;

new class extends Component {
    #[Reactive]
    public $todos;

    #[Computed]
    public function count()
    {
        return $this->todos->count();
    }
};
?>

<div>
    Count: {{ $this->count }}
</div>
```

Sekarang, todos yang ditambahkan atau dihapus di dalam komponen parent akan otomatis memicu update dalam komponen `todo-count`.

> **Catatan Performa:** Reactive properties adalah fitur yang sangat powerful, tetapi penting untuk memahami implikasi performanya dan hanya menambahkan `#[Reactive]` saat masuk akal untuk skenario khusus Anda.

---

## Slots

Slots memungkinkan Anda passing konten Blade dari komponen parent ke komponen child:

**Komponen Parent:**

```php
<?php

use Livewire\Attributes\Computed;
use Livewire\Component;
use App\Models\Post;

new class extends Component {
    public Post $post;

    #[Computed]
    public function comments()
    {
        return $this->post->comments;
    }

    public function removeComment($id)
    {
        $this->post->comments()->find($id)->delete();
    }
};
?>

<div>
    @foreach ($this->comments as $comment)
        <livewire:comment :$comment :wire:key="$comment->id">
            <button wire:click="removeComment({{ $comment->id }})">
                Remove
            </button>
        </livewire:comment>
    @endforeach
</div>
```

**Komponen Child:**

```php
<?php

use Livewire\Component;
use App\Models\Comment;

new class extends Component {
    public Comment $comment;
};
?>

<div>
    <p>{{ $comment->author }}</p>
    <p>{{ $comment->body }}</p>

    {{ $slot }}
</div>
```

> **Penting:** Slots dievaluasi dalam konteks komponen parent. Ini berarti properti atau method yang direferensikan di dalam slot milik parent, bukan child. Dalam contoh di atas, method `removeComment()` dipanggil pada komponen parent.

---

## Mendengarkan Event dari Children

Teknik komunikasi parent-child lain yang powerful adalah sistem event Livewire. Event memungkinkan Anda mendispatch event di server atau client yang dapat diintersep oleh komponen lain.

**Komponen Parent dengan event listener:**

```php
<?php
// resources/views/components/⚡todos.blade.php

use Illuminate\Support\Facades\Auth;
use Livewire\Attributes\Computed;
use Livewire\Attributes\On;
use Livewire\Component;
use App\Models\Todo;

new class extends Component {
    #[On('remove-todo')]
    public function remove($todoId)
    {
        $todo = Todo::find($todoId);
        $this->authorize('delete', $todo);
        $todo->delete();
    }

    #[Computed]
    public function todos()
    {
        return Auth::user()->todos;
    }
};
?>

<div>
    @foreach ($this->todos as $todo)
        <livewire:todo-item :$todo :wire:key="$todo->id" />
    @endforeach
</div>
```

**Komponen Child yang mendispatch event:**

```php
<?php
// resources/views/components/⚡todo-item.blade.php

use Livewire\Component;
use App\Models\Todo;

new class extends Component {
    public Todo $todo;

    public function remove()
    {
        $this->dispatch('remove-todo', todoId: $this->todo->id);
    }
};
?>

<div>
    <span>{{ $todo->content }}</span>
    <button wire:click="remove">Remove</button>
</div>
```

Sekarang saat tombol "Remove" diklik di dalam `todo-item`, komponen parent `todos` akan mengintersep event yang didispatch dan melakukan penghapusan todo.

Setelah todo dihapus di parent, list akan di-render ulang dan child yang mendispatch event `remove-todo` akan dihapus dari halaman.
