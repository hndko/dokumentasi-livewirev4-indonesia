# #[Transition]

Atribut `#[Transition]` mengaktifkan View Transitions API untuk komponen, memberikan animasi smooth saat DOM berubah.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Melewati Transisi](#melewati-transisi)
- [Parameters](#parameters)
- [Pendekatan Alternatif](#pendekatan-alternatif)

---

## Penggunaan Dasar

```php
use Livewire\Attributes\Transition;
use Livewire\Component;

#[Transition]
class PostList extends Component
{
    public function render()
    {
        return view('livewire.post-list');
    }
}
```

---

## Melewati Transisi

Untuk melewati transisi pada aksi tertentu:

```php
public function quickUpdate()
{
    $this->skipTransition();

    // Update tanpa transisi
}
```

---

## Parameters

```php
#[Transition(name: 'slide')]
class PostList extends Component
{
    // ...
}
```

Kemudian definisikan CSS:

```css
::view-transition-old(slide) {
  animation: slide-out 0.3s ease-out;
}

::view-transition-new(slide) {
  animation: slide-in 0.3s ease-in;
}
```

---

## Pendekatan Alternatif

### Menggunakan Method transition()

```php
public function render()
{
    return view('livewire.post-list')
        ->transition('slide');
}
```

### Menggunakan skipTransition()

```php
public function delete()
{
    $this->skipTransition();

    // ...
}
```
