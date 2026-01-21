# #[Computed]

Atribut `#[Computed]` menandai method sebagai computed property yang dapat diakses seperti properti dan secara otomatis di-cache selama satu request.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Keuntungan Performa](#keuntungan-performa)
- [Menghapus Cache](#menghapus-cache)
- [Caching Antar Request](#caching-antar-request)
- [Caching di Semua Komponen](#caching-di-semua-komponen)
- [Kapan Menggunakan](#kapan-menggunakan)
- [Reference](#reference)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Computed;
use Livewire\Component;

class ShowPosts extends Component
{
    #[Computed]
    public function posts()
    {
        return Post::all();
    }
}
```

```html
<div>
  @foreach ($this->posts as $post)
  <div>{{ $post->title }}</div>
  @endforeach
</div>
```

> **Penting:** Akses computed property dengan `$this->posts`, bukan `$posts`.

---

## Keuntungan Performa

Computed properties otomatis di-memoize - method hanya dipanggil sekali per request meskipun diakses berkali-kali.

---

## Menghapus Cache

```php
unset($this->posts);
```

---

## Caching Antar Request

```php
#[Computed(persist: true)]
public function posts()
{
    return Post::all();
}

// Dengan durasi custom (detik)
#[Computed(persist: true, seconds: 3600)]
public function posts()
{
    return Post::all();
}
```

---

## Caching di Semua Komponen

```php
#[Computed(cache: true)]
public function globalSettings()
{
    return Settings::all();
}

// Dengan key custom
#[Computed(cache: true, key: 'global-settings')]
public function globalSettings()
{
    return Settings::all();
}
```

---

## Kapan Menggunakan

- Query database yang mahal
- Kalkulasi kompleks
- Data yang diakses berkali-kali dalam satu render

---

## Reference

| Parameter | Deskripsi                |
| --------- | ------------------------ |
| `persist` | Cache antar request      |
| `seconds` | Durasi cache dalam detik |
| `cache`   | Cache di semua instance  |
| `key`     | Custom cache key         |
