# wire:submit

Livewire memudahkan penanganan pengiriman form via direktif `wire:submit`. Dengan menambahkan `wire:submit` ke elemen `<form>`, Livewire akan mencegat pengiriman form, mencegah handling browser default, dan memanggil method komponen Livewire.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Perilaku Default](#perilaku-default)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
class CreatePost extends Component
{
    public $title = '';
    public $content = '';

    public function save()
    {
        Post::create([
            'title' => $this->title,
            'content' => $this->content,
        ]);

        $this->redirect('/posts');
    }
}
```

```html
<form wire:submit="save">
  <input type="text" wire:model="title" />
  <textarea wire:model="content"></textarea>
  <button type="submit">Save</button>
</form>
```

Saat pengguna menekan "Save", `wire:submit` mencegat event submit dan memanggil aksi `save()` di server.

---

## Perilaku Default

`wire:submit` berbeda dari event handler Livewire lainnya karena secara internal memanggil `event.preventDefault()` tanpa perlu modifier `.prevent`.

Secara default, saat Livewire mengirim pengiriman form ke server:

- Tombol submit dinonaktifkan
- Semua input form ditandai sebagai `readonly`

Ini mencegah pengguna mengirim form yang sama lagi sampai pengiriman awal selesai.

---

## Reference

### Sintaks

```
wire:submit="methodName"
wire:submit="methodName(param1, param2)"
```

### Modifiers

| Modifier   | Deskripsi           |
| ---------- | ------------------- |
| `.prevent` | Otomatis diterapkan |
