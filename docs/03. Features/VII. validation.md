# Validation

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Validasi adalah bagian penting dari form handling. Livewire terintegrasi dengan sistem validasi Laravel untuk memberikan pengalaman validasi yang seamless dan real-time.

---

## Daftar Isi

- [Atribut Validate](#atribut-validate)
- [Form Objects](#form-objects)
- [Validasi Real-time](#validasi-real-time)
- [Menyesuaikan Pesan Error](#menyesuaikan-pesan-error)
- [Mendefinisikan Method rules()](#mendefinisikan-method-rules)
- [Menggunakan Laravel Rule Objects](#menggunakan-laravel-rule-objects)
- [Mengontrol Validation Errors Secara Manual](#mengontrol-validation-errors-secara-manual)
- [Mengakses Validator Instance](#mengakses-validator-instance)
- [Testing Validation](#testing-validation)
- [Mengakses Errors di JavaScript](#mengakses-errors-di-javascript)

---

## Atribut Validate

Cara paling sederhana untuk menambahkan validasi adalah dengan atribut `#[Validate]`:

```php
<?php

use Livewire\Attributes\Validate;
use Livewire\Component;

new class extends Component {
    #[Validate('required|min:5')]
    public $title = '';

    #[Validate('required')]
    public $content = '';

    public function save()
    {
        $this->validate();

        // Simpan data...
    }
};
```

### Custom Attribute Name

```php
#[Validate('required', as: 'judul postingan')]
public $title = '';
```

### Custom Validation Message

```php
#[Validate('required', message: 'Judul harus diisi')]
public $title = '';
```

### Custom Key

```php
#[Validate('required', key: 'form.title')]
public $title = '';
```

---

## Form Objects

Untuk form yang lebih kompleks, gunakan Form Objects:

```php
namespace App\Livewire\Forms;

use Livewire\Attributes\Validate;
use Livewire\Form;

class PostForm extends Form
{
    #[Validate('required|min:5')]
    public $title = '';

    #[Validate('required')]
    public $content = '';
}
```

```php
<?php

use App\Livewire\Forms\PostForm;
use Livewire\Component;

new class extends Component {
    public PostForm $form;

    public function save()
    {
        $this->form->validate();

        // Simpan menggunakan $this->form->title, $this->form->content
    }
};
```

---

## Validasi Real-time

Gunakan `wire:model.blur` untuk validasi saat pengguna meninggalkan field:

```html
<input wire:model.blur="title" type="text" />
<div>
  @error('title')
  <span class="error">{{ $message }}</span>
  @enderror
</div>
```

Atau `wire:model.live` untuk validasi saat mengetik:

```html
<input wire:model.live="title" type="text" />
```

---

## Menyesuaikan Pesan Error

### Custom Attribute Names

```php
public function messages()
{
    return [
        'title.required' => 'Judul harus diisi.',
        'content.required' => 'Konten tidak boleh kosong.',
    ];
}

public function validationAttributes()
{
    return [
        'title' => 'judul postingan',
        'content' => 'isi konten',
    ];
}
```

---

## Mendefinisikan Method rules()

```php
<?php

use Livewire\Component;

new class extends Component {
    public $title = '';
    public $content = '';

    public function rules()
    {
        return [
            'title' => 'required|min:5',
            'content' => 'required',
        ];
    }

    public function save()
    {
        $this->validate();
        // ...
    }
};
```

---

## Menggunakan Laravel Rule Objects

```php
use Illuminate\Validation\Rules\Password;
use Livewire\Attributes\Validate;

#[Validate([Password::min(8)->mixedCase()->numbers()])]
public $password = '';
```

---

## Mengontrol Validation Errors Secara Manual

### Menambahkan Error

```php
$this->addError('email', 'Email sudah digunakan.');
```

### Menghapus Error

```php
$this->resetErrorBag('email');

// Atau hapus semua
$this->resetErrorBag();
```

---

## Mengakses Validator Instance

```php
public function save()
{
    $validated = $this->withValidator(function ($validator) {
        $validator->after(function ($validator) {
            if ($this->somethingIsInvalid()) {
                $validator->errors()->add('field', 'Pesan error custom');
            }
        });
    })->validate();
}
```

---

## Testing Validation

```php
use Livewire\Livewire;

it('validates title is required', function () {
    Livewire::test('post.create')
        ->set('title', '')
        ->call('save')
        ->assertHasErrors(['title' => 'required']);
});

it('validates title min length', function () {
    Livewire::test('post.create')
        ->set('title', 'abc')
        ->call('save')
        ->assertHasErrors(['title' => 'min']);
});
```

---

## Mengakses Errors di JavaScript

Anda dapat mengakses error validasi via `$wire`:

```html
<div x-data>
  <input wire:model="title" type="text" />

  <template x-if="$wire.$errors.has('title')">
    <span x-text="$wire.$errors.first('title')" class="error"></span>
  </template>
</div>
```

### Method yang Tersedia

```javascript
// Mengecek apakah ada error
$wire.$errors.has("title");

// Mendapatkan error pertama
$wire.$errors.first("title");

// Mendapatkan semua error untuk field
$wire.$errors.get("title");

// Mendapatkan semua error
$wire.$errors.all();
```
