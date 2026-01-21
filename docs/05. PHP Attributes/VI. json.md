# #[Json]

Atribut `#[Json]` menandai method untuk mengembalikan respons JSON langsung ke JavaScript tanpa merender ulang komponen.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Menangani Response](#menangani-response)
- [Error Handling](#error-handling)
- [Kapan Menggunakan](#kapan-menggunakan)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Json;
use Livewire\Component;

class SearchComponent extends Component
{
    #[Json]
    public function search($query)
    {
        return [
            'results' => User::where('name', 'like', "%{$query}%")->get(),
            'count' => User::where('name', 'like', "%{$query}%")->count(),
        ];
    }
}
```

---

## Menangani Response

Di template, gunakan `$wire` dengan `.then()`:

```html
<div x-data="{ results: [] }">
  <input
    type="text"
    x-on:input.debounce="$wire.search($event.target.value).then(data => results = data.results)"
  />

  <template x-for="result in results">
    <div x-text="result.name"></div>
  </template>
</div>
```

---

## Error Handling

Jika method melempar exception, Promise akan direject:

```html
<button
  x-on:click="$wire.save()
        .then(data => alert('Berhasil!'))
        .catch(error => alert(error.message))"
>
  Save
</button>
```

---

## Kapan Menggunakan

- API-like interactions di dalam komponen
- Response data tanpa render ulang
- Integrasi dengan JavaScript/Alpine yang memerlukan data JSON
