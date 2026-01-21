# wire:intersect

Direktif `wire:intersect` memicu aksi saat elemen masuk ke viewport menggunakan Intersection Observer API.

---

## Daftar Isi

- [Penggunaan Dasar](#penggunaan-dasar)
- [Enter dan Leave Events](#enter-dan-leave-events)
- [Visibility Modifiers](#visibility-modifiers)
- [Margin](#margin)
- [Fire Once](#fire-once)
- [Use Cases](#use-cases)
- [Reference](#reference)

---

## Penggunaan Dasar

```html
<div wire:intersect="loadMore">
  <div wire:loading>Loading...</div>
</div>
```

---

## Enter dan Leave Events

```html
<div wire:intersect.enter="onVisible" wire:intersect.leave="onHidden">
  Content
</div>
```

---

## Visibility Modifiers

```html
<!-- Trigger saat 50% terlihat -->
<div wire:intersect.half="load">...</div>

<!-- Trigger saat 100% terlihat -->
<div wire:intersect.full="load">...</div>
```

---

## Margin

Tambahkan margin ke root:

```html
<div wire:intersect.margin.100px="load">...</div>
```

---

## Fire Once

Trigger hanya sekali:

```html
<div wire:intersect.once="loadMore">...</div>
```

---

## Use Cases

### Infinite Scroll

```html
@foreach ($posts as $post)
<div>{{ $post->title }}</div>
@endforeach

<div wire:intersect.once="loadMore">
  <div wire:loading>Loading more...</div>
</div>
```

### Lazy Loading Images

```html
<div wire:intersect.once="loadImage">
  @if ($imageLoaded)
  <img src="{{ $imageUrl }}" />
  @else
  <div class="placeholder">Loading...</div>
  @endif
</div>
```

---

## Reference

### Modifiers

| Modifier      | Deskripsi                    |
| ------------- | ---------------------------- |
| `.enter`      | Trigger saat masuk viewport  |
| `.leave`      | Trigger saat keluar viewport |
| `.once`       | Trigger hanya sekali         |
| `.half`       | Trigger saat 50% terlihat    |
| `.full`       | Trigger saat 100% terlihat   |
| `.margin.Xpx` | Tambahkan margin ke trigger  |
