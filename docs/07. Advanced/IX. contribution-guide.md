# Contribution Guide

Panduan untuk berkontribusi ke proyek Livewire.

---

## Daftar Isi

- [Setup Lokal](#setup-lokal)
- [Forking dan Cloning](#forking-dan-cloning)
- [Contributing a Failing Test](#contributing-a-failing-test)

---

## Setup Lokal

Untuk mengembangkan Livewire secara lokal, Anda perlu:

1. PHP 8.1+
2. Composer
3. Node.js dan NPM
4. Laravel application untuk testing

---

## Forking dan Cloning

1. Fork repository Livewire di GitHub
2. Clone fork Anda:

```bash
git clone https://github.com/YOUR-USERNAME/livewire.git
cd livewire
```

3. Install dependencies:

```bash
composer install
npm install
```

4. Link ke proyek Laravel:

```json
// composer.json di proyek Laravel
{
  "repositories": [
    {
      "type": "path",
      "url": "../livewire"
    }
  ]
}
```

---

## Contributing a Failing Test

Cara terbaik untuk melaporkan bug adalah dengan menyertakan test yang gagal:

1. Buat branch baru:

```bash
git checkout -b failing-test-description
```

2. Tulis test di folder yang sesuai:

```php
// tests/Browser/SomeFeatureTest.php
public function test_something_that_should_work()
{
    Livewire::test(SomeComponent::class)
        ->set('property', 'value')
        ->assertSee('expected output');
}
```

3. Pastikan test gagal:

```bash
php artisan test --filter test_something_that_should_work
```

4. Commit dan push:

```bash
git add .
git commit -m "Failing test: description of the issue"
git push origin failing-test-description
```

5. Buat Pull Request dengan deskripsi masalah
