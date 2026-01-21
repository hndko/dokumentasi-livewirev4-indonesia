# wire:model

Livewire memudahkan binding nilai properti komponen dengan input form menggunakan `wire:model`.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menyesuaikan Timing Update](#menyesuaikan-timing-update)
  - [Live Updating](#live-updating)
  - [Updating on Blur](#updating-on-blur)
  - [Updating on Change](#updating-on-change)
- [Tipe Input](#tipe-input)
  - [Text Inputs](#text-inputs)
  - [Textarea](#textarea)
  - [Checkboxes](#checkboxes)
  - [Radio Buttons](#radio-buttons)
  - [Select Dropdowns](#select-dropdowns)
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

Nilai input akan disinkronkan dengan properti server saat tombol "Save" ditekan.

---

## Menyesuaikan Timing Update

Secara default, Livewire hanya mengirim network request saat aksi dilakukan (`wire:click` atau `wire:submit`), BUKAN saat input `wire:model` diperbarui.

### Live Updating

Untuk mengirim update properti saat pengguna mengetik:

```html
<input type="text" wire:model.live="title" />
```

**Menyesuaikan debounce** (default 150ms):

```html
<input type="text" wire:model.live.debounce.250ms="title" />
```

### Updating on Blur

Update server saat pengguna meninggalkan input:

```html
<input type="text" wire:model.blur="title" />
```

### Updating on Change

Update server saat nilai berubah (untuk select):

```html
<select wire:model.change="category">
  <!-- options -->
</select>
```

---

## Tipe Input

### Text Inputs

```html
<input type="text" wire:model="title" />
```

### Textarea

```html
<textarea wire:model="content"></textarea>
```

### Checkboxes

**Single checkbox (boolean):**

```html
<input type="checkbox" wire:model="receiveUpdates" />
```

**Multiple checkboxes (array):**

```html
<input type="checkbox" value="email" wire:model="updateTypes" />
<input type="checkbox" value="sms" wire:model="updateTypes" />
```

### Radio Buttons

```html
<input type="radio" value="yes" wire:model="receiveUpdates" />
<input type="radio" value="no" wire:model="receiveUpdates" />
```

### Select Dropdowns

```html
<select wire:model="category">
  <option value="">Pilih kategori</option>
  <option value="news">News</option>
  <option value="tutorials">Tutorials</option>
</select>
```

**Multi-select:**

```html
<select wire:model="categories" multiple>
  <option value="news">News</option>
  <option value="tutorials">Tutorials</option>
</select>
```

---

## Reference

### Modifiers

| Modifier        | Deskripsi                |
| --------------- | ------------------------ |
| `.live`         | Update saat mengetik     |
| `.blur`         | Update saat blur         |
| `.change`       | Update saat change event |
| `.debounce.Xms` | Debounce update          |
| `.throttle.Xms` | Throttle update          |
| `.number`       | Cast ke number           |
| `.boolean`      | Cast ke boolean          |
| `.fill`         | Isi dari request         |
