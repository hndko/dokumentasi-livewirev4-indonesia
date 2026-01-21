# #[Validate]

Atribut `#[Validate]` mendefinisikan aturan validasi untuk properti komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Cara Kerjanya](#cara-kerjanya)
- [Validasi Real-time](#validasi-real-time)
- [Custom Attribute Names](#custom-attribute-names)
- [Custom Messages](#custom-messages)
- [Array Validation](#array-validation)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Validate;
use Livewire\Component;

class CreatePost extends Component
{
    #[Validate('required|min:5')]
    public $title = '';

    #[Validate('required')]
    public $content = '';

    public function save()
    {
        $this->validate();

        Post::create([
            'title' => $this->title,
            'content' => $this->content,
        ]);
    }
}
```

---

## Cara Kerjanya

Saat `$this->validate()` dipanggil, Livewire memvalidasi semua properti dengan atribut `#[Validate]`.

---

## Validasi Real-time

```html
<input wire:model.blur="title" /> @error('title')
<span>{{ $message }}</span> @enderror
```

Dengan `wire:model.blur`, validasi dijalankan saat pengguna meninggalkan field.

---

## Custom Attribute Names

```php
#[Validate('required', as: 'judul postingan')]
public $title = '';
```

Error message: "Judul postingan harus diisi."

---

## Custom Messages

```php
#[Validate('required', message: 'Judul tidak boleh kosong.')]
public $title = '';
```

---

## Array Validation

```php
#[Validate(['required', 'array', 'min:1'])]
public $tags = [];

#[Validate(['tags.*' => 'required|string'])]
public $tags = [];
```

---

## Kapan Menggunakan

- Form validation
- Input sanitization
- Data integrity checks

---

## Reference

| Parameter | Deskripsi                           |
| --------- | ----------------------------------- |
| `rule`    | Aturan validasi (string atau array) |
| `as`      | Custom attribute name               |
| `message` | Custom error message                |
| `key`     | Custom validation key               |
