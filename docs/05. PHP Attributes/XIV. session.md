# #[Session]

Atribut `#[Session]` menyimpan properti di session Laravel, mempertahankan nilai antar request dan antar halaman.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Cara Kerjanya](#cara-kerjanya)
- [Session vs URL](#session-vs-url)
- [Custom Session Keys](#custom-session-keys)
- [Dynamic Session Keys](#dynamic-session-keys)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Session;
use Livewire\Component;

class Dashboard extends Component
{
    #[Session]
    public $filter = 'all';

    #[Session]
    public $sortBy = 'created_at';
}
```

Nilai akan dipertahankan bahkan setelah pengguna meninggalkan halaman dan kembali.

---

## Cara Kerjanya

- Saat properti diubah, nilai otomatis disimpan ke session
- Saat komponen dimount, nilai diambil dari session
- Default value digunakan jika tidak ada nilai di session

---

## Session vs URL

| Atribut      | Kegunaan                                      |
| ------------ | --------------------------------------------- |
| `#[Session]` | State persisten, tidak perlu di-share via URL |
| `#[Url]`     | State yang dapat di-bookmark dan di-share     |

---

## Custom Session Keys

```php
#[Session(key: 'dashboard_filter')]
public $filter = 'all';
```

---

## Dynamic Session Keys

```php
#[Session(key: 'filter_{userId}')]
public $filter = 'all';

public $userId;
```

---

## Kapan Menggunakan

- Preferensi pengguna (sort order, view mode)
- Filter yang harus diingat
- State UI yang persisten

---

## Reference

| Parameter | Deskripsi          |
| --------- | ------------------ |
| `key`     | Custom session key |
