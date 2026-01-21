# Understanding Nesting

Dokumen ini menjelaskan bagaimana komponen bersarang bekerja di Livewire dan implikasi performa dari berbagai pendekatan.

---

## Daftar Isi

- [Setiap Komponen Independen](#setiap-komponen-independen)
- [Updating Child](#updating-child)
- [Updating Parent](#updating-parent)
- [Implikasi Performa](#implikasi-performa)

---

## Setiap Komponen Independen

Di Livewire, setiap komponen adalah "island" - entitas independen dengan state dan lifecycle sendiri.

```html
<!-- Parent -->
<div>
  <h1>Parent Component</h1>
  <livewire:child-component :userId="$userId" />
</div>
```

Meskipun child dirender di dalam parent, mereka memiliki:

- Request/response terpisah
- State terpisah
- Lifecycle hooks terpisah

---

## Updating Child

Saat child diupdate:

1. Hanya child yang mengirim request ke server
2. Hanya child yang dirender ulang
3. Parent **tidak** dirender ulang

```php
// Child component
public function increment()
{
    $this->count++;
    // Hanya child yang dirender ulang
}
```

---

## Updating Parent

Saat parent diupdate:

1. Parent dirender ulang
2. Child **tidak** dirender ulang secara default
3. Props yang dikirim ke child diperbarui, tapi child tidak memproses perubahan kecuali menggunakan `#[Reactive]`

```php
// Parent component
public function refresh()
{
    $this->data = User::all();
    // Child tidak dirender ulang
}
```

---

## Implikasi Performa

### Keuntungan Arsitektur Ini

- Komponen child tidak memblokir render parent
- Update lebih efisien (hanya komponen yang diperlukan)
- Mencegah cascade render yang tidak perlu

### Pertimbangan

- Komunikasi antar komponen memerlukan events
- Props tidak reaktif secara default (gunakan `#[Reactive]` jika diperlukan)
- Setiap komponen = request terpisah
