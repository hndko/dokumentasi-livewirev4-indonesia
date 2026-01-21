# wire:ref

Direktif `wire:ref` membuat referensi ke elemen DOM yang dapat diakses dari PHP atau JavaScript.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Dispatching Events](#dispatching-events)
- [Mengakses DOM Elements](#mengakses-dom-elements)
- [Streaming Content](#streaming-content)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<input type="text" wire:ref="input" />
<button wire:click="focusInput">Focus</button>
```

```php
public function focusInput()
{
    $this->refs->input->focus();
}
```

---

## Dispatching Events

```php
$this->refs->modal->dispatch('open');
```

---

## Mengakses DOM Elements

Di JavaScript menggunakan `$wire`:

```html
<div x-data>
  <input type="text" wire:ref="search" />
  <button x-on:click="$wire.$refs.search.focus()">Focus</button>
</div>
```

---

## Streaming Content

Berguna untuk streaming konten ke elemen tertentu:

```php
$this->stream(
    to: 'output',
    content: $chunk,
);
```

```html
<div wire:ref="output"></div>
```

---

## Reference

### Sintaks

```
wire:ref="name"
```
