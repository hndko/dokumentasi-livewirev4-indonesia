# File Uploads

[Mulai belajar di Laracasts](https://laracasts.com/series/everything-new-in-livewire-4)

Livewire menyediakan cara yang mudah untuk menangani upload file. Dengan menggunakan trait `WithFileUploads`, Anda dapat mengikat input file ke properti komponen dan menyimpan file yang diupload.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menyimpan File yang Diupload](#menyimpan-file-yang-diupload)
- [Menangani Multiple Files](#menangani-multiple-files)
- [Validasi File](#validasi-file)
- [URL Preview Sementara](#url-preview-sementara)
- [Indikator Loading](#indikator-loading)
- [Indikator Progress](#indikator-progress)
- [Upload Langsung ke Amazon S3](#upload-langsung-ke-amazon-s3)
- [Testing File Uploads](#testing-file-uploads)
- [Konfigurasi](#konfigurasi)

---

## Penggunaan Dasar

```php
<?php

use Livewire\WithFileUploads;
use Livewire\Component;

new class extends Component {
    use WithFileUploads;

    public $photo;

    public function save()
    {
        $this->validate([
            'photo' => 'image|max:1024', // 1MB Max
        ]);

        $this->photo->store('photos');
    }
};
?>

<form wire:submit="save">
    <input type="file" wire:model="photo">

    @error('photo')
        <span class="error">{{ $message }}</span>
    @enderror

    <button type="submit">Upload</button>
</form>
```

---

## Menyimpan File yang Diupload

```php
// Simpan ke disk default dengan nama acak
$this->photo->store('photos');

// Simpan ke disk "s3"
$this->photo->store('photos', 's3');

// Simpan dengan nama khusus
$this->photo->storeAs('photos', 'photo.jpg');

// Simpan secara publik
$this->photo->storePublicly('photos');
```

---

## Menangani Multiple Files

```php
<?php

use Livewire\WithFileUploads;
use Livewire\Component;

new class extends Component {
    use WithFileUploads;

    public $photos = [];

    public function save()
    {
        $this->validate([
            'photos.*' => 'image|max:1024',
        ]);

        foreach ($this->photos as $photo) {
            $photo->store('photos');
        }
    }
};
?>

<form wire:submit="save">
    <input type="file" wire:model="photos" multiple>

    <button type="submit">Upload</button>
</form>
```

---

## Validasi File

```php
<?php

use Livewire\Attributes\Validate;
use Livewire\WithFileUploads;
use Livewire\Component;

new class extends Component {
    use WithFileUploads;

    #[Validate('image|max:1024|mimes:jpg,png')]
    public $photo;

    public function save()
    {
        $this->validate();

        $this->photo->store('photos');
    }
};
```

---

## URL Preview Sementara

```html
<div>
  @if ($photo)
  <img src="{{ $photo->temporaryUrl() }}" />
  @endif

  <input type="file" wire:model="photo" />
</div>
```

> **Catatan:** `temporaryUrl()` hanya berfungsi untuk file gambar.

---

## Indikator Loading

```html
<input type="file" wire:model="photo" />

<div wire:loading wire:target="photo">Uploading...</div>
```

---

## Indikator Progress

```html
<input type="file" wire:model="photo" />

<div
  x-data="{ progress: 0 }"
  x-on:livewire-upload-start="progress = 0"
  x-on:livewire-upload-progress="progress = $event.detail.progress"
  x-on:livewire-upload-finish="progress = 100"
>
  <progress max="100" x-bind:value="progress"></progress>
</div>
```

---

## Upload Langsung ke Amazon S3

Konfigurasikan di `config/livewire.php`:

```php
'temporary_file_upload' => [
    'disk' => 's3',
],
```

### Membersihkan File Sementara

File sementara dibersihkan otomatis setelah 24 jam. Anda dapat menyesuaikan ini:

```php
'temporary_file_upload' => [
    'disk' => 's3',
    'directory' => 'livewire-tmp',
    'rules' => ['required', 'file', 'max:5000'],
    'delete_after' => 60 * 60 * 24, // 24 jam dalam detik
],
```

---

## Testing File Uploads

```php
use Illuminate\Http\UploadedFile;
use Livewire\Livewire;

it('can upload a photo', function () {
    Storage::fake('photos');

    $file = UploadedFile::fake()->image('photo.jpg');

    Livewire::test('upload-photo')
        ->set('photo', $file)
        ->call('save');

    Storage::disk('photos')->assertExists('photo.jpg');
});
```

---

## Konfigurasi

### Validasi Global

Di `config/livewire.php`:

```php
'temporary_file_upload' => [
    'rules' => ['required', 'file', 'max:5000'],
],
```

### Middleware Global

```php
'temporary_file_upload' => [
    'middleware' => ['auth'],
],
```

### Direktori Upload Sementara

```php
'temporary_file_upload' => [
    'directory' => 'livewire-tmp',
],
```
