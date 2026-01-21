# wire:transition

Direktif `wire:transition` menggunakan View Transitions API untuk menganimasikan perubahan DOM.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Named Transitions](#named-transitions)
- [Customizing Animations](#customizing-animations)
- [Skipping Transitions](#skipping-transitions)
- [Browser Support](#browser-support)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:transition>{{ $content }}</div>
```

---

## Named Transitions

Untuk menganimasikan elemen tertentu:

```html
<div wire:transition="fade">{{ $content }}</div>
```

---

## Customizing Animations

```css
::view-transition-old(fade) {
  animation: fade-out 0.3s ease-out;
}

::view-transition-new(fade) {
  animation: fade-in 0.3s ease-in;
}

@keyframes fade-out {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
```

---

## Skipping Transitions

Gunakan `$skipTransition` untuk melewati transisi:

```php
$this->skipTransition();
```

---

## Browser Support

View Transitions API didukung di Chrome 111+ dan Edge 111+. Firefox dan Safari memiliki dukungan terbatas.

Livewire secara otomatis menghormati preferensi `prefers-reduced-motion`.

---

## Reference

### Sintaks

```
wire:transition
wire:transition="name"
```
