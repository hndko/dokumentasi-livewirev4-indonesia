# Step 4: Membuat View Komponen

## 🎯 Tujuan

- Membuat tampilan untuk komponen TodoList
- Menggunakan directive wire:model, wire:click, wire:submit
- Menampilkan loading states

---

## 📝 Langkah 4.1: Buka File View

Buka `resources/views/livewire/todo-list.blade.php`

File default kosong atau hanya berisi `<div>`.

---

## 📝 Langkah 4.2: Struktur Dasar HTML

```html
<div class="max-w-md mx-auto mt-10 p-6 bg-white rounded-lg shadow-lg">
  <h1 class="text-2xl font-bold text-center mb-6">📝 Todo List</h1>

  <!-- Form akan ditambahkan di sini -->

  <!-- Daftar todo akan ditambahkan di sini -->

  <!-- Counter akan ditambahkan di sini -->
</div>
```

---

## 📝 Langkah 4.3: Form Tambah Todo

Tambahkan form setelah `<h1>`:

```html
<!-- Form Tambah Todo -->
<form wire:submit="addTodo" class="mb-6">
  <div class="flex gap-2">
    <input
      type="text"
      wire:model="newTodo"
      placeholder="Tambah todo baru..."
      class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
    />
    <button
      type="submit"
      class="px-6 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
    >
      <span wire:loading.remove wire:target="addTodo">Tambah</span>
      <span wire:loading wire:target="addTodo">⏳</span>
    </button>
  </div>

  <!-- Error Message -->
  @error('newTodo')
  <p class="text-red-500 text-sm mt-2">{{ $message }}</p>
  @enderror
</form>
```

### Penjelasan Directive:

| Directive               | Fungsi                            |
| ----------------------- | --------------------------------- |
| `wire:submit="addTodo"` | Panggil method saat form disubmit |
| `wire:model="newTodo"`  | Bind input ke property            |
| `wire:loading`          | Tampilkan saat loading            |
| `wire:loading.remove`   | Sembunyikan saat loading          |
| `wire:target="addTodo"` | Target action tertentu            |

---

## 📝 Langkah 4.4: Daftar Todo

Tambahkan setelah form:

```html
<!-- Daftar Todo -->
<ul class="space-y-2">
  @forelse ($todos as $todo)
  <li
    wire:key="{{ $todo->id }}"
    class="flex items-center gap-3 p-3 bg-gray-50 rounded-lg hover:bg-gray-100 transition-colors"
  >
    <!-- Checkbox -->
    <input
      type="checkbox"
      wire:click="toggleComplete({{ $todo->id }})"
      {{
      $todo-
    />completed ? 'checked' : '' }} class="w-5 h-5 text-blue-500 rounded
    focus:ring-blue-500 cursor-pointer" >

    <!-- Title -->
    <span
      class="{{ $todo->completed ? 'line-through text-gray-400' : 'text-gray-700' }} flex-1"
    >
      {{ $todo->title }}
    </span>

    <!-- Delete Button -->
    <button
      wire:click="deleteTodo({{ $todo->id }})"
      wire:confirm="Yakin ingin menghapus '{{ $todo->title }}'?"
      class="text-red-500 hover:text-red-700 transition-colors"
    >
      🗑️
    </button>
  </li>
  @empty
  <li class="text-gray-400 text-center py-8">
    <div class="text-4xl mb-2">📋</div>
    <p>Belum ada todo. Tambahkan sekarang!</p>
  </li>
  @endforelse
</ul>
```

### Penjelasan:

| Directive      | Fungsi                                               |
| -------------- | ---------------------------------------------------- |
| `wire:key`     | Identifier unik untuk setiap item (WAJIB dalam loop) |
| `wire:click`   | Panggil method dengan parameter                      |
| `wire:confirm` | Dialog konfirmasi sebelum aksi                       |

---

## 📝 Langkah 4.5: Counter

Tambahkan di bagian bawah:

```html
<!-- Counter -->
@if ($todos->count() > 0)
<div
  class="mt-6 pt-4 border-t border-gray-200 flex justify-between text-sm text-gray-500"
>
  <span>Total: {{ $todos->count() }} todo</span>
  <span>Selesai: {{ $todos->where('completed', true)->count() }}</span>
</div>
@endif
```

---

## 📝 Langkah 4.6: Kode Lengkap

File `resources/views/livewire/todo-list.blade.php`:

```html
<div class="max-w-md mx-auto mt-10 p-6 bg-white rounded-lg shadow-lg">
  <h1 class="text-2xl font-bold text-center mb-6">📝 Todo List</h1>

  <!-- Form Tambah Todo -->
  <form wire:submit="addTodo" class="mb-6">
    <div class="flex gap-2">
      <input
        type="text"
        wire:model="newTodo"
        placeholder="Tambah todo baru..."
        class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
      />
      <button
        type="submit"
        class="px-6 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
      >
        <span wire:loading.remove wire:target="addTodo">Tambah</span>
        <span wire:loading wire:target="addTodo">⏳</span>
      </button>
    </div>
    @error('newTodo')
    <p class="text-red-500 text-sm mt-2">{{ $message }}</p>
    @enderror
  </form>

  <!-- Daftar Todo -->
  <ul class="space-y-2">
    @forelse ($todos as $todo)
    <li
      wire:key="{{ $todo->id }}"
      class="flex items-center gap-3 p-3 bg-gray-50 rounded-lg hover:bg-gray-100 transition-colors"
    >
      <input
        type="checkbox"
        wire:click="toggleComplete({{ $todo->id }})"
        {{
        $todo-
      />completed ? 'checked' : '' }} class="w-5 h-5 text-blue-500 rounded
      focus:ring-blue-500 cursor-pointer" >
      <span
        class="{{ $todo->completed ? 'line-through text-gray-400' : 'text-gray-700' }} flex-1"
      >
        {{ $todo->title }}
      </span>
      <button
        wire:click="deleteTodo({{ $todo->id }})"
        wire:confirm="Yakin ingin menghapus '{{ $todo->title }}'?"
        class="text-red-500 hover:text-red-700 transition-colors"
      >
        🗑️
      </button>
    </li>
    @empty
    <li class="text-gray-400 text-center py-8">
      <div class="text-4xl mb-2">📋</div>
      <p>Belum ada todo. Tambahkan sekarang!</p>
    </li>
    @endforelse
  </ul>

  <!-- Counter -->
  @if ($todos->count() > 0)
  <div
    class="mt-6 pt-4 border-t border-gray-200 flex justify-between text-sm text-gray-500"
  >
    <span>Total: {{ $todos->count() }} todo</span>
    <span>Selesai: {{ $todos->where('completed', true)->count() }}</span>
  </div>
  @endif
</div>
```

---

## ✅ Checkpoint

Setelah step ini, Anda harus memiliki:

- [x] View dengan form input
- [x] Daftar todo dengan checkbox dan delete
- [x] Loading states
- [x] Counter

---

## ⬅️ [Step 3: Komponen Livewire](03-komponen-livewire.md) | ➡️ [Step 5: Layout & Route](05-layout-route.md)
