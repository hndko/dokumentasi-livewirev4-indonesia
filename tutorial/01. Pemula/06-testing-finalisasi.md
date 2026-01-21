# Step 6: Testing & Finalisasi

## 🎯 Tujuan

- Menguji semua fitur aplikasi
- Memahami flow data di Livewire
- Menyelesaikan project

---

## 📝 Langkah 6.1: Test Tambah Todo

1. Buka `http://localhost:8000`
2. Ketik "Belajar Livewire" di input
3. Klik tombol "Tambah"

**Yang terjadi:**

- `wire:model` mengikat input ke `$newTodo`
- `wire:submit` memanggil method `addTodo()`
- Livewire mengirim request ke server
- Data disimpan ke database
- Komponen dirender ulang dengan data baru

---

## 📝 Langkah 6.2: Test Validasi

1. Coba submit form kosong
2. Anda akan melihat error "Todo minimal 3 karakter"
3. Coba ketik "ab" (kurang dari 3 karakter)
4. Error masih muncul

**Yang terjadi:**

- `#[Validate]` attribute memvalidasi input
- Error ditampilkan dengan `@error` directive

---

## 📝 Langkah 6.3: Test Toggle Complete

1. Tambahkan beberapa todo
2. Klik checkbox pada salah satu todo
3. Todo akan memiliki strikethrough dan warna abu-abu

**Yang terjadi:**

- `wire:click="toggleComplete({{ $todo->id }})"` memanggil method dengan ID
- Database diupdate
- UI dirender ulang

---

## 📝 Langkah 6.4: Test Delete

1. Klik icon 🗑️ pada salah satu todo
2. Dialog konfirmasi muncul
3. Klik OK untuk menghapus

**Yang terjadi:**

- `wire:confirm` menampilkan dialog native browser
- Jika dikonfirmasi, `deleteTodo()` dipanggil
- Todo dihapus dari database

---

## 📝 Langkah 6.5: Test Loading State

1. Buka Network tab di DevTools
2. Throttle ke "Slow 3G"
3. Tambahkan todo baru
4. Perhatikan tombol berubah menjadi "⏳"

**Yang terjadi:**

- `wire:loading` menampilkan loading indicator
- `wire:loading.remove` menyembunyikan teks "Tambah"

---

## 📝 Langkah 6.6: Menulis Test Otomatis (Opsional)

Buat file test:

```bash
php artisan make:test TodoListTest
```

Edit `tests/Feature/TodoListTest.php`:

```php
<?php

namespace Tests\Feature;

use App\Livewire\TodoList;
use App\Models\Todo;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Livewire\Livewire;
use Tests\TestCase;

class TodoListTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_create_todo()
    {
        Livewire::test(TodoList::class)
            ->set('newTodo', 'Test Todo')
            ->call('addTodo')
            ->assertSee('Test Todo');

        $this->assertDatabaseHas('todos', [
            'title' => 'Test Todo',
        ]);
    }

    public function test_todo_title_is_required()
    {
        Livewire::test(TodoList::class)
            ->set('newTodo', '')
            ->call('addTodo')
            ->assertHasErrors(['newTodo' => 'required']);
    }

    public function test_can_toggle_complete()
    {
        $todo = Todo::create(['title' => 'Test']);

        Livewire::test(TodoList::class)
            ->call('toggleComplete', $todo->id);

        $this->assertTrue($todo->fresh()->completed);
    }

    public function test_can_delete_todo()
    {
        $todo = Todo::create(['title' => 'Test']);

        Livewire::test(TodoList::class)
            ->call('deleteTodo', $todo->id);

        $this->assertDatabaseMissing('todos', ['id' => $todo->id]);
    }
}
```

Jalankan test:

```bash
php artisan test --filter TodoListTest
```

---

## 📝 Langkah 6.7: Struktur Final Project

```
todo-app/
├── app/
│   ├── Livewire/
│   │   └── TodoList.php          ← Komponen Livewire
│   └── Models/
│       └── Todo.php              ← Model Eloquent
├── database/
│   └── migrations/
│       └── xxxx_create_todos_table.php
├── resources/
│   └── views/
│       ├── components/
│       │   └── layouts/
│       │       └── app.blade.php ← Layout
│       └── livewire/
│           └── todo-list.blade.php ← View komponen
├── routes/
│   └── web.php                   ← Route
└── tests/
    └── Feature/
        └── TodoListTest.php      ← Test (opsional)
```

---

## 🎉 Selamat!

Anda telah berhasil membuat aplikasi Todo List dengan Livewire v4!

### Yang Sudah Dipelajari:

| Konsep         | Deskripsi                         |
| -------------- | --------------------------------- |
| Komponen       | Class + View yang bekerja bersama |
| Properties     | State yang reaktif                |
| Actions        | Method yang dipanggil dari view   |
| Data Binding   | `wire:model` untuk input          |
| Event Handling | `wire:click`, `wire:submit`       |
| Validasi       | `#[Validate]` attribute           |
| Loading States | `wire:loading` directive          |
| Confirmation   | `wire:confirm` dialog             |

---

## 🚀 Tantangan Lanjutan

1. **Edit Todo** - Tambahkan fitur inline edit
2. **Filter** - Tambahkan filter (All/Active/Completed)
3. **Deadline** - Tambahkan tanggal deadline
4. **Categories** - Tambahkan kategori dengan relasi

---

## ⬅️ [Step 5: Layout & Route](05-layout-route.md) | 🏠 [Kembali ke README](README.md)
