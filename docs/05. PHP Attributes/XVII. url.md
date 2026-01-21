# #[Url]

Atribut `#[Url]` menyinkronkan properti dengan URL query parameter, memungkinkan state yang dapat di-bookmark dan di-share.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Cara Kerjanya](#cara-kerjanya)
- [URL vs Session](#url-vs-session)
- [Menggunakan Alias](#menggunakan-alias)
- [Mengecualikan Nilai](#mengecualikan-nilai)
- [Selalu Tampilkan di URL](#selalu-tampilkan-di-url)
- [Browser History](#browser-history)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Url;
use Livewire\Component;

class PostList extends Component
{
    #[Url]
    public $search = '';

    #[Url]
    public $page = 1;
}
```

URL akan diperbarui menjadi: `?search=hello&page=2`

---

## Cara Kerjanya

- Saat properti berubah, URL diperbarui
- Saat halaman dimuat, properti diinisialisasi dari URL
- Default value digunakan jika parameter tidak ada

---

## URL vs Session

| Atribut      | Kegunaan                                  |
| ------------ | ----------------------------------------- |
| `#[Url]`     | State yang dapat di-bookmark dan di-share |
| `#[Session]` | State persisten, tidak perlu di-share     |

---

## Menggunakan Alias

```php
#[Url(as: 'q')]
public $search = '';
```

URL: `?q=hello` alih-alih `?search=hello`

---

## Mengecualikan Nilai

```php
#[Url(except: '')]
public $search = '';
```

Parameter dihapus dari URL saat nilainya kosong.

---

## Selalu Tampilkan di URL

```php
#[Url(keep: true)]
public $search = '';
```

---

## Browser History

```php
#[Url(history: true)]
public $search = '';
```

Menggunakan `pushState` alih-alih `replaceState`.

---

## Kapan Menggunakan

- Search/filter yang dapat di-share
- Pagination
- Tab selection
- State yang perlu di-bookmark

---

## Reference

| Parameter | Deskripsi                        |
| --------- | -------------------------------- |
| `as`      | Alias query parameter            |
| `except`  | Nilai yang dikecualikan dari URL |
| `keep`    | Selalu tampilkan di URL          |
| `history` | Gunakan pushState                |
