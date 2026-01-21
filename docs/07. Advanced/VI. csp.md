# CSP (Content Security Policy)

Panduan untuk menggunakan Livewire dengan Content Security Policy yang ketat.

---

## Daftar Isi

- [Apa itu CSP](#apa-itu-csp)
- [Mengapa CSP Mempengaruhi Livewire](#mengapa-csp-mempengaruhi-livewire)
- [Mengaktifkan CSP-Safe Mode](#mengaktifkan-csp-safe-mode)
- [Yang Didukung](#yang-didukung)
- [Yang Tidak Didukung](#yang-tidak-didukung)
- [Workarounds](#workarounds)

---

## Apa itu CSP

Content Security Policy adalah header HTTP yang membatasi sumber konten yang dapat dimuat browser, mencegah serangan XSS.

```http
Content-Security-Policy: script-src 'self'; style-src 'self'
```

---

## Mengapa CSP Mempengaruhi Livewire

Livewire dan Alpine menggunakan evaluasi JavaScript dinamis (`eval()`). CSP ketat memblokir ini.

---

## Mengaktifkan CSP-Safe Mode

### Konfigurasi

```php
// config/livewire.php
'csp_safe' => true,
```

Atau via environment:

```env
LIVEWIRE_CSP_SAFE=true
```

---

## Yang Didukung

### Ekspresi Livewire Dasar

```html
<button wire:click="save">Save</button> <input wire:model="title" />
```

### Method Calls dengan Parameter

```html
<button wire:click="delete({{ $id }})">Delete</button>
```

### Property Access dan Updates

```html
<button wire:click="$set('count', 5)">Set</button>
```

### Ekspresi Alpine Dasar

```html
<div x-data="{ open: false }">
  <button x-on:click="open = !open">Toggle</button>
</div>
```

---

## Yang Tidak Didukung

### Ekspresi JavaScript Kompleks

```html
<!-- ❌ Tidak didukung -->
<button wire:click="save(items.filter(i => i.active))">Save</button>
```

### Template Literals

```html
<!-- ❌ Tidak didukung -->
<div x-text="`Hello ${name}`"></div>
```

### Dynamic Property Access

```html
<!-- ❌ Tidak didukung -->
<button wire:click="update(data[key])">Update</button>
```

---

## Workarounds

Pindahkan logika kompleks ke method PHP:

```php
public function saveActiveItems()
{
    $active = collect($this->items)->filter->active;
    // ...
}
```

```html
<button wire:click="saveActiveItems">Save</button>
```
