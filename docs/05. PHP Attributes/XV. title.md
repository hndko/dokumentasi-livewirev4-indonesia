# #[Title]

Atribut `#[Title]` menetapkan judul halaman untuk komponen full-page.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Konfigurasi Layout](#konfigurasi-layout)
- [Dynamic Titles](#dynamic-titles)
- [Kombinasi dengan Layout](#kombinasi-dengan-layout)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Title;
use Livewire\Component;

#[Title('Dashboard')]
class Dashboard extends Component
{
    public function render()
    {
        return view('livewire.dashboard');
    }
}
```

---

## Konfigurasi Layout

Layout Anda harus memiliki slot untuk title:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>{{ $title ?? 'Default Title' }}</title>
  </head>
  <body>
    {{ $slot }}
  </body>
</html>
```

---

## Dynamic Titles

Untuk title dinamis, gunakan method `title()`:

```php
class ShowPost extends Component
{
    public Post $post;

    public function render()
    {
        return view('livewire.show-post')
            ->title($this->post->title);
    }
}
```

---

## Kombinasi dengan Layout

```php
#[Title('Admin Dashboard')]
#[Layout('layouts.admin')]
class AdminDashboard extends Component
{
    // ...
}
```

---

## Kapan Menggunakan

- Full-page components
- Halaman dengan judul statis
- SEO optimization

---

## Reference

| Parameter | Deskripsi     |
| --------- | ------------- |
| `title`   | Judul halaman |

> **Catatan:** Atribut ini hanya berlaku untuk komponen full-page.
