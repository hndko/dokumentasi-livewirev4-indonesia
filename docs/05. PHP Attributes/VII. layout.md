# #[Layout]

Atribut `#[Layout]` menentukan layout file yang digunakan saat komponen dirender sebagai halaman penuh.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Default Layout](#default-layout)
- [Passing Data ke Layout](#passing-data-ke-layout)
- [Layout Berbeda per Halaman](#layout-berbeda-per-halaman)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Layout;
use Livewire\Component;

#[Layout('layouts.app')]
class Dashboard extends Component
{
    public function render()
    {
        return view('livewire.dashboard');
    }
}
```

---

## Default Layout

Secara default, Livewire menggunakan `layouts.app`. Anda dapat mengubah ini di `config/livewire.php`:

```php
'layout' => 'layouts.main',
```

---

## Passing Data ke Layout

```php
#[Layout('layouts.app', ['title' => 'Dashboard'])]
class Dashboard extends Component
{
    // ...
}
```

Atau dinamis:

```php
public function render()
{
    return view('livewire.dashboard')
        ->layout('layouts.app', ['title' => 'Dashboard']);
}
```

---

## Layout Berbeda per Halaman

```php
#[Layout('layouts.admin')]
class AdminDashboard extends Component
{
    // ...
}

#[Layout('layouts.guest')]
class LoginPage extends Component
{
    // ...
}
```

---

## Kapan Menggunakan

- Full-page components
- Halaman yang memerlukan layout berbeda dari default
- Admin vs public pages

---

## Reference

| Parameter | Deskripsi               |
| --------- | ----------------------- |
| `view`    | Nama view layout        |
| `params`  | Array data untuk layout |
