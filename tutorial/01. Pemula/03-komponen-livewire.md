# Step 3: Membuat Komponen Livewire

## 🎯 Tujuan

- Membuat komponen Livewire pertama
- Memahami struktur komponen
- Menambahkan properties dan actions

---

## 📝 Langkah 3.1: Generate Komponen

Jalankan perintah Artisan:

```bash
php artisan make:livewire TodoList
```

Output:

```
COMPONENT CREATED 🤙

CLASS: app/Livewire/TodoList.php
VIEW:  resources/views/livewire/todo-list.blade.php
```

Livewire membuat 2 file:

1. **Class** - Logika komponen (PHP)
2. **View** - Tampilan komponen (Blade)

---

## 📝 Langkah 3.2: Buka File Komponen

Buka `app/Livewire/TodoList.php`. File default:

```php
<?php

namespace App\Livewire;

use Livewire\Component;

class TodoList extends Component
{
    public function render()
    {
        return view('livewire.todo-list');
    }
}
```

---

## 📝 Langkah 3.3: Tambahkan Property untuk Input

Tambahkan property `$newTodo` dengan validasi:

```php
<?php

namespace App\Livewire;

use App\Models\Todo;
use Livewire\Attributes\Validate;
use Livewire\Component;

class TodoList extends Component
{
    #[Validate('required|min:3', message: 'Todo minimal 3 karakter')]
    public $newTodo = '';

    public function render()
    {
        return view('livewire.todo-list');
    }
}
```

### Penjelasan:

- `public $newTodo = ''` - Property yang bisa di-bind ke input
- `#[Validate(...)]` - Atribut PHP 8 untuk validasi otomatis

---

## 📝 Langkah 3.4: Tambahkan Method untuk Menambah Todo

```php
public function addTodo()
{
    // Validasi input
    $this->validate();

    // Simpan ke database
    Todo::create([
        'title' => $this->newTodo,
    ]);

    // Reset input
    $this->newTodo = '';
}
```

---

## 📝 Langkah 3.5: Tambahkan Method Toggle Complete

```php
public function toggleComplete($id)
{
    $todo = Todo::find($id);
    $todo->update([
        'completed' => !$todo->completed,
    ]);
}
```

---

## 📝 Langkah 3.6: Tambahkan Method Delete

```php
public function deleteTodo($id)
{
    Todo::find($id)->delete();
}
```

---

## 📝 Langkah 3.7: Update Render untuk Mengambil Data

```php
public function render()
{
    return view('livewire.todo-list', [
        'todos' => Todo::latest()->get(),
    ]);
}
```

---

## 📝 Langkah 3.8: Kode Lengkap

File `app/Livewire/TodoList.php`:

```php
<?php

namespace App\Livewire;

use App\Models\Todo;
use Livewire\Attributes\Validate;
use Livewire\Component;

class TodoList extends Component
{
    #[Validate('required|min:3', message: 'Todo minimal 3 karakter')]
    public $newTodo = '';

    /**
     * Menambahkan todo baru
     */
    public function addTodo()
    {
        $this->validate();

        Todo::create([
            'title' => $this->newTodo,
        ]);

        $this->newTodo = '';
    }

    /**
     * Toggle status completed
     */
    public function toggleComplete($id)
    {
        $todo = Todo::find($id);
        $todo->update([
            'completed' => !$todo->completed,
        ]);
    }

    /**
     * Menghapus todo
     */
    public function deleteTodo($id)
    {
        Todo::find($id)->delete();
    }

    /**
     * Render komponen
     */
    public function render()
    {
        return view('livewire.todo-list', [
            'todos' => Todo::latest()->get(),
        ]);
    }
}
```

---

## 🔍 Penjelasan Konsep

### Properties

```php
public $newTodo = '';
```

- Properties adalah state komponen
- Otomatis tersedia di view
- Bisa di-bind dengan `wire:model`

### Actions (Methods)

```php
public function addTodo() { ... }
```

- Method public bisa dipanggil dari view
- Dipanggil dengan `wire:click="addTodo"`

### Validasi

```php
#[Validate('required|min:3')]
```

- Menggunakan PHP 8 Attributes
- Otomatis divalidasi saat `$this->validate()`

---

## ✅ Checkpoint

Setelah step ini, Anda harus memiliki:

- [x] Komponen `TodoList` dengan logic lengkap
- [x] Method: addTodo, toggleComplete, deleteTodo
- [x] Property dengan validasi

---

## ⬅️ [Step 2: Database & Model](02-database-model.md) | ➡️ [Step 4: View Komponen](04-view-komponen.md)
